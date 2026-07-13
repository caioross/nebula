# Interface

Status: draft — sketches to be attached before this is marked stable · This document specifies observable behavior. Implementation details (shaders, easing curves as code) come with M1.

## One surface

Nebula opens as a single borderless region of calm color — near-white or near-black, following the system appearance until the user expresses a preference in words. There is no title bar furniture beyond what the operating system legally requires, no icons, no status bar, no cursor changes that hint at hidden click targets. The window is not a container of controls. It is paper.

Two regions exist, and the user should never perceive the line between them, because there isn't one:

- **The cloud** — the writing region, anchored to the top. At rest it occupies roughly the top tenth of the surface, growing as the user writes, never beyond a fifth. It has no box, no border, no background fill of its own. It is defined only by where text appears when you type: the caret sits there from the first frame, already focused. Typing is the only onboarding.
- **The response surface** — everything below. Results materialize here by fading in, anchored under the words that caused them. Pages render here. Answers stream here. Incoming messages surface here.

No divider separates the two. When a response is long, it flows upward *under* the cloud's region as the user reads, the way content passes under a car's windshield — the cloud remains legible above it with a soft gradient veil, never an opaque bar.

## The cloud's states

The cloud is the only animated actor in the interface, and its motion is the entire feedback vocabulary. Four states:

**Resting.** Empty surface, caret breathing slowly (a 3–4 s opacity cycle, subtle). First launch adds a single line of small text beneath the caret — *write anything* — which fades permanently after the first keystroke of the user's life with Nebula and never returns.

**Receiving.** The user is typing. Text renders instantly (the latency budget is in the [roadmap](../ROADMAP.md), M1 exit conditions). The field grows downward line by line; earlier lines compress slightly in size as the field grows, keeping the newest words largest. Nothing else on the surface moves while the user types.

**Thinking.** Input has been dispatched. The written text loosens — letter spacing eases apart a few percent, opacity drops toward 70% — and a slow luminous drift moves through the cloud region, like light through fog. The effect must read as *alive, working* from across a room, and must never loop visibly. Duration honesty matters: the drift's intensity tracks actual work, and if the router resolved the input in milliseconds, thinking may be skipped entirely. No spinners, no progress bars, no percentage lies.

**Delivering.** The response fades in below over 200–400 ms while the cloud's text condenses back to rest. The user's words from that turn shrink and rise into the turn history — reachable by scrolling up within the cloud region — leaving the field clear for the next thought.

## Motion rules

1. Everything arrives and leaves by opacity, sometimes with a few pixels of vertical drift. Nothing slides in from edges, bounces, or scales up from a point. The register is fog and paper, not springs.
2. One thing moves at a time. If the response is fading in, the cloud is already still.
3. Motion is information. Every animation answers a user question — *did it hear me? is it working? where did my thing go?* Decoration that answers no question is cut.
4. All motion respects the platform's reduced-motion setting, replaced by immediate state changes.

## No buttons, and what replaces them

The rule is absolute for the shipped product: no clickable control drawn by Nebula anywhere, ever. What other programs put in chrome, Nebula handles in language, and the language surface must therefore be excellent at the jobs buttons usually do:

| Button job | Nebula equivalent |
|---|---|
| Close / quit | "bye", "close", "shut down" — and the OS window close, which is never disabled |
| Settings | "make it dark", "smaller text", "speak Portuguese" |
| Back / history | "go back", "show me that contract from yesterday" |
| Stop | typing anything new preempts; "stop" halts everything |
| Scroll / expand | natural scroll gestures still work; "collapse that", "show it all" |
| Copy / save | "copy that", "save this as a file on my desktop" |

Two honest exceptions to purity, because safety beats ideology:

- **Content is still content.** Links inside a rendered web page are clickable, form fields on a page are typeable. The no-control rule governs Nebula's own surface, not documents it displays. (Even so, every page interaction has a written equivalent: "click the second result".)
- **The OS is still the OS.** Window management, Alt-F4, Cmd-Q, accessibility services — all untouched. Nebula never traps the user.

## Language of the machine

Nebula's own voice on the canvas follows fixed rules: it confirms consequential actions in one short sentence before doing them when they are destructive or externally visible ("This sends the message to Marcos — go ahead?"), it reports completed work in the past tense without ceremony, and it never emits the vocabulary of the industry. The words *model, token, agent, prompt, context, plugin, LLM* do not appear on the canvas unless the user says them first. If the user asks "which model answered that?", Nebula answers plainly — capability is never hidden from those who ask — but it does not volunteer machinery.

## Appearance

Typography carries the whole interface, so it is specified early: one serif or humanist face for the user's words and responses (reading register), one compact sans for Nebula's own short confirmations (machine register), sizes fluid between 16 px and 22 px baseline depending on surface size and user request. Line length capped near 70 characters regardless of window width. Both appearances — light and dark — are designed together from day one; neither is an afterthought inversion, and the switch between them is itself a fade.

Color is almost absent. The surface, the two text registers, one accent used only by the thinking drift and link underlines. Semantic color (red for danger) appears only in confirmation sentences for destructive acts.

## Accessibility

The no-button rule is an accessibility gift if honored properly: the entire interface is text, which is exactly what screen readers, switch inputs and voice control consume best. Commitments: full accessibility-tree exposure of the cloud and response surface, every visual state announced textually, complete keyboard operation by construction, and the reduced-motion behavior above. Voice input is a post-M5 roadmap item, but nothing in this spec may assume it away.

## What this document rules out

For avoidance of doubt in future reviews: tabs, docks, launchers, notification badges, tray icons with menus, tooltips, hamburger anything, onboarding carousels, and modal dialogs of every kind. If a future feature seems to need one of these, the feature is misdesigned; bring it back as language.
