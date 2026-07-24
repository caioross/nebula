# Architecture

Status: draft — open questions listed at the end · Companion documents: [routing](routing.md), [models](models.md), [web-engine](web-engine.md), [interface](interface.md), [connectors](connectors.md)

## Overview

Nebula is a single native process built in Rust ([ADR-0001](decisions/0001-rust.md)), organized as five subsystems around one shared spine. Nothing is a plugin; every subsystem is part of the core and compiled into the one binary the user receives.

```
 ┌─────────────────────────────────────────────────────┐
 │  Canvas            GPU-rendered surface, the cloud   │
 └───────────────▲─────────────────────────────────────┘
                 │ events / frames
 ┌───────────────┴─────────────────────────────────────┐
 │  Spine             typed message bus + session state │
 ├──────────┬──────────────┬──────────────┬────────────┤
 │  Router  │ Orchestrator │  Web engine  │ Connectors │
 │  intent  │ local+remote │  render +    │ WhatsApp,  │
 │  triage  │ model fleet  │  structure   │ providers  │
 ├──────────┴──────────────┴──────────────┴────────────┤
 │  Vault             folder-contained state, memory,   │
 │                    credentials, model cache          │
 └─────────────────────────────────────────────────────┘
```

The flow for every keystroke of user input is the same: the canvas emits text to the spine; the router classifies it; the spine dispatches to the subsystem the router chose; results stream back over the spine to the canvas, which renders them below the cloud. Every subsystem speaks only to the spine — no lateral calls — so each can be developed, tested and replaced against a fixed contract.

## The spine

A typed, in-process message bus plus the session state machine. Messages are enums, not strings; every payload that crosses a subsystem boundary has a schema defined in one crate (`nebula-proto`) that all subsystems depend on. The spine owns:

- **Session state.** What is currently displayed, what the active context is (a page, a conversation, a document), and the history of turns. Subsystems are stateless with respect to the session; they receive context, they don't hold it.
- **Cancellation.** The user can write over anything. New input preempts running work by default; the spine propagates cancellation tokens so a superseded page load or model call stops burning resources.
- **The audit line.** Every dispatch and result is journaled to the vault. This is what lets the user ask "what did you do?" and get a truthful answer, and it is the substrate for undo.

## Router

The router answers one question — *what kind of thing did the user just write?* — cheaply, locally, and fast enough to be invisible. It is deterministic first, statistical second, generative never. Full specification in [routing.md](routing.md).

Output is an `Intent` value — one of `Navigate(url)`, `Command(verb, args)`, `Task(class, context_refs)`, `Message(target, body?)`, `Continuation(turn_ref)` or `Ambiguous(candidates)`. routing.md defines this set and is authoritative; the enum above is just the overview. Ambiguity is resolved by asking the user in one short sentence, never by guessing between destructive alternatives.

## Orchestrator

Owns the model fleet: the bundled local models, any user-connected remote providers, and the policy for choosing among them per task class. It exposes a single `complete(task) -> stream` interface to the spine; everything about which engine runs, with what context window, at what quantization, is its internal business. Full specification in [models.md](models.md).

The orchestrator is also the trust boundary for context assembly. It decides what enters a model's context — and, critically, tags every span of that context as *user words*, *Nebula system text* or *foreign content* (pages, messages, files). Foreign content is structurally quarantined; see the injection defense in [web-engine.md](web-engine.md#reading-without-obeying).

## Web engine

Two responsibilities that most software treats as one: **showing** pages and **understanding** them. Rendering paints the page onto the canvas region below the cloud. Understanding produces a parallel structural representation — element tree, roles, text, actionable targets with stable identifiers — that the orchestrator can hand to models as data. Acting on a page ("click the export button") resolves through those identifiers, not through screen coordinates. Strategy and the build-vs-embed decision in [ADR-0003](decisions/0003-web-engine.md); full specification in [web-engine.md](web-engine.md).

## Connectors

Inbound and outbound bridges to the world: messaging services (WhatsApp first) and model providers. Connectors are established, repaired and removed through conversation — "connect my WhatsApp", "remove my OpenAI key" — and never through any interface of their own. Incoming events surface on the canvas through the same spine path as everything else. Full specification in [connectors.md](connectors.md).

## Vault

Everything Nebula persists lives under one directory, the way a browser profile does:

```
nebula/
├── models/        verified weights, content-addressed
├── memory/        long-term user memory (structured + embedded)
├── credentials/   provider keys, connector sessions — encrypted at rest
├── journal/       audit line, session history
└── state/         appearance, learned preferences
```

Rules the vault enforces regardless of which subsystem is writing:

1. Credentials are encrypted with a key held by the OS keychain where one is running, and with a passphrase-derived key where none is; they never appear in the journal, in model context, or in any log, and the recognizer that detects "this looks like a key" runs before journaling, not after. No key ever ships inside the binary.
2. Nothing under `nebula/` is ever transmitted except as an explicit part of a user-requested action, and the journal records what was sent, where, when.
3. The user can say "forget what I told you about X" or "delete everything" and the corresponding erasure is real, including embeddings.

The keychain-less branch is Linux in practice, and it is where most desktop software quietly gives up: the common fallback is AES under a password compiled into the executable, which leaves a rule like the one above readable as a guarantee while making it untrue. Nebula asks instead, once, at the moment the first credential would be written. Not at startup, because a fresh install has an empty `credentials/` and can stay that way indefinitely, so nothing needs protecting until the user hands over a secret, and by then there is a conversation with room for one more sentence. Two honest answers fit in it: derive a key from a passphrase, unlocked once per session, or decline to store the credential and re-enter it when it is needed. The vault records which of the three modes is in force, so "what is protecting my keys?" is answerable in a sentence like everything else.

What a keychain buys is not the same on every platform, and rule 1 should not be read as one uniform promise. macOS binds items to the requesting binary and Windows DPAPI binds them to the user account. The Secret Service binds them to the session and offers no isolation between processes running as that user, which gnome-keyring treats as a deliberate position rather than a gap, so on Linux a running keychain protects credentials against another user of the machine and against the disk, not against other software the user is already running. [SECURITY.md](../SECURITY.md) hedges threat 2 with "to the extent the platform allows". That is what the phrase costs, platform by platform.

## Concurrency model

One async runtime (tokio). The canvas has a dedicated render thread with a frame budget that nothing else may borrow against — model inference, page layout and network I/O run on worker pools, and their results cross to the render thread as immutable frame data. Local inference gets a bounded number of compute threads negotiated at startup against the machine's capabilities; the router's small model is memory-resident always, larger models load and unload under the orchestrator's policy.

## Failure philosophy

Every failure has a sentence. Subsystem errors carry a user-facing explanation written at the site of the failure, in plain words, and the canvas renders that sentence in the response area like any other result. "I couldn't reach that site." "That model needs more memory than this machine has free — I used the smaller one instead." No error codes on the canvas, no dialogs, and no failure that leaves the cloud unresponsive. The process supervises its subsystems; a crashed subsystem restarts with its state rebuilt from the journal.

## Platform targets

Windows, macOS and Linux from M1, one codebase, native windowing per platform behind a thin layer (`winit` or equivalent — final choice is an M1 implementation detail, not an ADR). Mobile is out of scope until after M5 and will be a companion, not a port; the interface model assumes a keyboard-first surface until the mobile RFC argues otherwise.

## Open questions

Recorded here until they sharpen into tracker issues; the significant ones as of this revision:

1. Whether the journal should be append-only with periodic compaction or a normal database with history tables — bears on "forget me" guarantees.
2. Whether connectors run in the main process or in a sandboxed child process per connector. Isolation argues for children; the WhatsApp session model may force the decision.
