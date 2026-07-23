# Routing

Status: draft · Depends on: [architecture](architecture.md) · Feeds: [models](models.md)

## The problem

Every piece of text the user writes must become exactly one of a small set of intents, in under 50 ms for the common cases, without a network round trip, and without ever asking the user to learn syntax. The router is what makes "one field for everything" viable — it is the difference between Nebula and a chat window.

Design position: **deterministic first, statistical second, generative never.** Big models are terrible arbiters of what the user meant by *cheap* standards — slow, non-reproducible, unauditable. The router therefore never uses a generative model to decide routing. Generation is what happens *after* routing.

## Intents

```
Navigate(url)                     open a destination
Command(verb, args)               operate Nebula itself
Task(class, context_refs)        produce or transform something
Message(target, body?)            communicate through a connector
Continuation(turn_ref)            addendum to the previous turn
Ambiguous(candidates)             genuinely unclear — ask, in one sentence
```

`Task.class` is the coarse workload label the orchestrator prices engines against: `answer`, `write`, `transform`, `code`, `page_action`, `file_action`. The class list is closed; extending it is an RFC-level change because every class must have a routing signature, an engine policy and an evaluation set.

## Three stages

### Stage 1 — Structure (deterministic, ~0 ms)

Pure functions, no models, exhaustively unit-tested:

- **Destination shapes.** Bare domains (`github.com`), full URLs, `localhost:3000`, IP literals, and "shaped like a domain with a typo" (`githbu.com`) which routes to Navigate with a written correction offer rather than a search guess.
- **The command lexicon.** A closed, versioned vocabulary of operational verbs in every shipped language — close, stop, back, darker, louder, forget, connect, disconnect — matched against short inputs (≤ 6 words) with strict patterns. The lexicon is data (`router/lexicon/*.toml` eventually), reviewed like code, because it *is* the settings surface of the product.
- **Hard signals.** Provider key formats (`sk-...` and friends) route to the credential flow and are masked from that moment; quoted reply-to-message patterns route to Message when a conversation context is active.

Anything Stage 1 resolves never touches a model. Measured expectation: a majority of real-world inputs end here (navigations and short commands dominate actual usage of such fields).

### Stage 2 — Classification (small local model, target < 30 ms)

A compact classifier — target under 500 MB in memory, quantized, always resident — fine-tuned for exactly two judgments:

1. Intent class, over the closed set above.
2. Task class, when the intent is Task.

It sees the current input plus a thin context summary (is a page open? is a conversation active? what was the last turn?), because "translate this" means nothing without knowing what *this* is. The last turn reaches it as a descriptor: that there was one, whether its result is still the one on the response surface, its intent and its class. Never the turn's own content. The classifier judges the shape of an input, and content stays behind the boundary Stage 3 draws below. It outputs a distribution, not a label.

Confidence policy: above the high threshold, dispatch; between thresholds, dispatch the leader but hedge in the response phrasing ("Here's a summary of the page — if you meant something else, say so"); below the low threshold, `Ambiguous` — Nebula asks one short question with the two leading readings as options, in words. Thresholds are per-class and set from the evaluation corpus, not by feel.

Telling a Continuation from a new Task is the sharpest case, and it doesn't turn on how recently the user typed. "Shorter", "now in English", "add a clause about pets": none of them names what it operates on. An input that can't stand alone, and that resolves against no page and no conversation, is addressing the only other thing in the room — the previous turn. Recency is the precondition rather than the test. Continuation is a legal reading while the previous turn is still addressable, meaning its result is the one on the response surface ([interface.md](interface.md#the-clouds-states)); that expires when a new turn replaces it, not after some count of seconds, so someone who reads a long answer for four minutes and then writes "shorter" is still understood. With nothing addressable and an input that still can't stand alone, the reading is `Ambiguous` and one short question. Never a fresh Task on an invented object: losing a referent costs a sentence, inventing one costs a wrong action on the wrong thing.

One rule this stage deliberately does not own: anything that sends, deletes or spends confirms first, regardless of confidence. Stage 2 cannot evaluate that predicate. It sees class labels, and `page_action` covers "scroll down" and "pay this invoice on the page" under the same label; whether an action spends is not knowable until Stage 3 resolves the element, or later still. The classifier can mark a reading as candidate-destructive, but that mark is a hint, not a gate. The binding confirmation is enforced at dispatch, from the structured action, on the spine's confirmed-action path — [web-engine.md](web-engine.md#reading-without-obeying) (layer 2) is the source of truth for that rule. Destructive facts surface at every stage (`forget` resolves in Stage 1's lexicon, a payment button only in Stage 3), and dispatch is the single point that sees them all.

### Stage 3 — Enrichment (same small model, only for Task)

For Task intents, resolve the references: which page region "this" points to, which prior turn "that contract" names, which file "the photo I dropped" means. Output is `context_refs` — stable identifiers into session state — never copied content. The orchestrator, not the router, decides what content those references pull into a model context, because that is where the trust boundary is enforced.

## Learning the user, carefully

The router improves per user, on device only. Corrections are the signal: when the user says "no, I meant open it", the (input, correct intent) pair enters a local adaptation store, applied as a lightweight bias layer over the classifier — never a silent full fine-tune, and exportable/erasable like all vault data. There is no telemetry of inputs; the published evaluation set grows from synthetic data and volunteered examples, not from users' fields.

## The evaluation set

`router/eval/` (from M2) contains the public corpus every router change is measured against: thousands of labeled inputs per language, including adversarial shapes — URLs that look like sentences, sentences that look like URLs, commands embedded in dictation, keys pasted mid-sentence. Accuracy gates are in the roadmap exit criteria. A routing change that improves feel but drops the score doesn't ship until the score is repaired.

## Open questions

1. Whether Stage 2's model is one multilingual classifier or per-language heads behind a language detector — memory vs. accuracy trade, needs measurement.
2. Where the Continuation threshold sits. The rule in Stage 2 is deterministic about when a Continuation is even available; whether a given input can stand alone is still the classifier's call, and the number to measure is the false-continuation rate over pairs of identical words, one with an addressable turn behind them and one without.
3. Whether the command lexicon should accept user-taught synonyms ("when I say 'kill it' I mean stop") in v1 or defer personal vocabulary to the memory system.
