# Web Engine

Status: draft · Depends on: [architecture](architecture.md), [ADR-0003](decisions/0003-web-engine.md)

## Two jobs, deliberately separated

Browsers conflate showing a page with understanding it, because for a browser the user's eyes do the understanding. Nebula cannot afford that conflation: its models need to know what a page *is*, not what it looks like. So the engine has two outputs from one load:

1. **The painted page** — rendered into the response surface, scrollable, readable, its links and fields live. Visually it is a page on paper: no browser chrome of any kind surrounds it, no address bar (the cloud already is one), no tab strip, no padlock iconography — security state is expressed in words when it matters ("this connection isn't private — still want it?").
2. **The structural read** — a parallel representation built from the live document: the element tree with computed roles, visible text, landmark structure, and every actionable element (links, buttons, fields) with a stable identifier, its accessible name and its state. This is what models receive. It is also what page actions execute against: "click the export button" resolves to an identifier from the structural read, and the engine dispatches a real event to a real element. No pixel coordinates, no screenshot parsing, no guessing.

The structural read is not an afterthought serialization; it is a first-class output with its own test suite, versioned schema, and size discipline (large pages are read hierarchically — landmarks first, regions expanded on demand — because context windows are finite and most of a page is boilerplate).

## Rendering strategy

Building a from-scratch engine that renders the modern web is a decade of work; the arithmetic and the options are laid out honestly in [ADR-0003](decisions/0003-web-engine.md). The accepted position:

- Nebula embeds **Servo** as its rendering core — Rust-native, embeddable, actively developed, and philosophically aligned (independent of the Chromium monoculture). Nebula builds its own compositing of Servo's output into the canvas, its own navigation logic, and the structural-read pipeline against Servo's DOM.
- The embedding is wrapped behind an internal trait (`PageEngine`) sized so that a different engine could replace Servo without touching the rest of Nebula. This keeps the ADR reversible, which such a large external dependency must be.
- Pages that Servo cannot yet render acceptably fail with a sentence, and the structural read is attempted anyway — for many tasks ("what does this article say?") understanding survives rendering imperfections. Compatibility grows release by release; the reference page set in `webengine/pages/` (from M3) defines "acceptable" measurably.

What Nebula does not do: ship Chromium, ship a WebView dependency on the platform's browser, or present itself as a general-purpose browser competing on compatibility benchmarks. The web is an ability, and the compatibility target is "the pages people actually need in daily life", grown empirically from real failures reported through the canvas ("that site didn't load right" files structured feedback, with consent, in words).

## Reading without obeying

Every page is untrusted input to a system that can act. This is the project's hardest security problem ([SECURITY.md](../SECURITY.md), threat 1), and the defense is layered:

**Layer 1 — Provenance tagging.** Everything from the engine enters model context as `foreign` spans (see [models.md](models.md#context-assembly-and-the-quarantine-rule)). The tagging is structural — it survives summarization, quotation and translation, because derived spans inherit provenance.

**Layer 2 — Capability gating.** A model call that has foreign content in context runs with reduced capabilities by default: it can produce text and propose actions, but proposed actions that leave the machine (sending, submitting, purchasing, connecting) require confirmation through the canvas in Nebula's own voice.

Foreign content is one reason to confirm. It is not the only one. Confirmation before dispatch answers to two independent triggers, and either one alone is enough. The first is the act itself: anything that sends, spends, deletes, or is otherwise destructive or externally visible confirms first, no matter where the instruction came from — a purely user-authored "tell Marcos I'll be late", with no page open and nothing foreign in context, confirms exactly as a page-driven send does, because the user can misspeak and the router can misread. The second is provenance: foreign content in the context that produced the action confirms it even when the act would otherwise be routine, which is the injection case above. This layer is the source of truth for both. In either case the confirmation sentence is generated from the *structured action*, never from model prose, so a compromised generation can't write its own permission slip.

**Layer 3 — Action allow-listing per origin.** Page actions execute only against elements of the currently displayed page, and cross-origin consequences (a form that posts elsewhere, a link that leaves the site) are stated in the confirmation when they are detectable.

**Layer 4 — Adversarial corpus.** `webengine/attacks/` (from M3) collects known injection patterns — invisible text, instruction-shaped content, confusable UI — and the M3 exit gate requires the shipped defenses to hold against all of it. The corpus is public; attackers already know these techniques, and defenders finding gaps is worth more than obscurity.

## Sessions, storage, identity

Nebula keeps per-site state (cookies, local storage) in the vault, partitioned by origin, wiped by sentence ("forget everything about that site"). There is no account system, no sync, no browsing history sold to anyone — the journal's page-visit records are vault data under the same erasure guarantees as everything else. Passwords for sites are out of scope until a dedicated RFC; the engine will not half-build a password manager.

## Open questions

1. Servo's embedding API surface is still moving, so the `PageEngine` trait gets designed against a pinned revision early in M3 and the upgrade cadence is decided there too. That second half is narrower than it was: Servo now publishes an LTS line with stated backport and migration windows, which turns an open field into one candidate to accept or reject against the cadence [ADR-0003](decisions/0003-web-engine.md) promised.
2. How much JavaScript-driven app interactivity (docs editors, dashboards) is inside the compatibility target for M3 versus explicitly deferred. The reference page set answers this by construction: what the set contains is what the target is. So the work here is assembling that set, which nobody has started.
3. Whether the structural read should also power a reader mode ("just show me the article") in M3 for free, since the data is already there. Likely yes; needs an interface-spec addendum for how it's asked for and rendered.
