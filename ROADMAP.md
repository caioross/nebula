# Roadmap

Milestones, not dates. Each phase has an exit condition; the project does not move on until it is met. Phases are numbered for reference from issues and RFCs (`M0`, `M1`, ...).

## M0 — Specification (current)

Everything is designed before anything is built. The specifications in [docs/](docs) must be complete enough that two competent engineers reading them independently would build compatible systems.

Exit condition: every document in `docs/` has reached "stable" status (see the header of each file), the open questions listed inside them are resolved or explicitly deferred, and the decision records cover language, rendering strategy, model runtime and licensing.

## M1 — The canvas

A native window on Windows, macOS and Linux: GPU-rendered canvas, the cloud (input field) with its full motion behavior, text rendering, light and dark appearance, and the fade-based result surface. No intelligence yet — the canvas echoes input so the interface can be judged on feel alone.

Exit condition: the interface spec is implemented to the letter; input latency under 10 ms from keystroke to glyph on mid-range hardware; a person who has never seen Nebula starts writing within five seconds of launch without instruction.

## M2 — Understanding

The intent router. Address detection, command lexicon, language detection, and the small local classifier that separates "open this", "do this" and "answer this". Bundled model runtime with one small and one mid-size model, downloaded and verified on first run, selected automatically per request.

Exit condition: router decisions under 50 ms for the deterministic path; measured routing accuracy above 95% on the published evaluation set; the machine works with the network cable unplugged.

## M3 — The web

Page rendering inside the canvas and, more importantly, structural reading: the engine exposes the page's content and interactive elements to the models as data, so "pay this invoice on the page" resolves to real elements, not guesses over pixels.

Exit condition: the reference page set renders usably; structural extraction passes its test suite; the injection defenses specified in the security model hold against the published attack corpus.

## M4 — Reach

Connected providers ("connect my OpenAI key") through conversation alone, and the first messaging connector, WhatsApp, with incoming messages surfacing on the canvas. Model swapping by request ("use the smaller model", "get me something better at code").

Exit condition: a non-technical user connects a provider and receives a WhatsApp message end-to-end without touching anything but the keyboard.

## M5 — Release

Packaging, signing, auto-update, the project website with downloads, and the first public build. Versioning follows semver from here; release notes are written for humans.

## Beyond

Mobile companion, additional messaging connectors, voice input, and long-term memory that lets Nebula genuinely learn its user. Each of these enters the roadmap only through an accepted RFC.
