# 0003 — Rendering: embed Servo behind an internal trait

Status: accepted

## Context

Nebula must display web pages on its canvas and extract their structure for its models. The founding instinct — build everything ourselves, depend on nobody's browser — collides here with arithmetic: a rendering engine compatible with the modern web is thousands of person-years (Blink, Gecko and WebKit are among the largest codebases in civilian software). Pretending otherwise would doom the project honestly described as its opposite.

The decision is therefore about where independence actually lives. Nebula's independence is its interface model, its router, its orchestrator and its structural-read pipeline — not the reimplementation of CSS grid.

## Options considered

**Build a rendering engine from scratch.** Full alignment with the from-zero ethos, total control, and a guarantee the project dies rendering flexbox before anyone ever types into the cloud. Rejected on arithmetic.

**Embed Chromium (CEF) or platform WebViews.** Maximum compatibility immediately. Rejected: shipping Chromium makes Nebula a Chrome costume — enormous binary, a dependency treadmill controlled by Google, and platform WebViews reintroduce per-OS behavioral drift while both make the structural read a fight against an engine designed for other goals.

**Embed Servo.** Rust-native, genuinely embeddable, independent of the Chromium monoculture, under active stewardship, and philosophically a sibling: a from-scratch engine built by people who believed the web needed one. Compatibility trails the big three — real cost, accepted and mitigated below.

**Structure-only (no visual rendering).** Radically simple, and wrong: seeing the page is part of the product's promise.

## Decision

Embed Servo as the rendering core. Nebula owns everything around it: compositing Servo's output into the canvas, navigation, session state, and the structural-read pipeline. All Servo contact goes through one internal trait (`PageEngine`) with its own conformance tests, sized so the engine could be replaced — including, in an ambitious future, by our own — without touching the rest of the system. Compatibility is managed empirically against a public reference page set that defines "good enough" per milestone, weighted toward the pages Nebula's actual audience needs daily.

## Costs accepted

Some pages will render imperfectly for years; the product communicates such failures in plain sentences and leans on the structural read, which often survives rendering gaps. Servo's embedding API is young and moving — pinned revisions and a deliberate upgrade cadence are part of M3's engineering budget. Upstream patches Nebula needs get contributed to Servo, which is a cost that is also a citizenship.
