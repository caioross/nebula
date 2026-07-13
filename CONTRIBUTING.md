# Contributing to Nebula

Nebula is being designed in public before it is built. Right now the most valuable contributions are not patches — they are careful readings of the specifications, hard questions, and well-argued proposals. When implementation begins, this document will grow the practical sections (building, testing, code style). Until then, here is how the project works.

## Ground rules

The bar is high on purpose. Nebula's premise is that one person with no technical background should be able to use the whole system through plain writing. Every proposal is measured against that premise. A feature that requires the user to learn a concept, click a control, or edit a file is out of scope no matter how useful it would be.

Second rule: proposals are text. If an idea can't survive being written down clearly, it isn't ready. We don't do design by chat thread.

Third rule: read before writing. Most questions are answered in [docs/](docs). Issues and RFCs that contradict a decision record without addressing its reasoning will be closed with a pointer to the record.

## How to propose something

**Small and concrete** (a wording fix, an inconsistency between two documents, a missing edge case in a spec): open an issue using the appropriate template.

**Substantial** (a new capability, a change to the interface model, an architectural alternative): write an RFC. Copy `docs/rfcs/0000-template.md`, fill it in, and open a pull request adding it to `docs/rfcs/`. Discussion happens on the pull request. An RFC is accepted when the maintainer merges it, and its number becomes permanent.

**Questions and open-ended discussion** belong in GitHub Discussions, not issues. Issues are for actionable, specific items.

## What gets accepted

An RFC has a real chance if it:

- respects the founding constraints (language as the only control, local-first, no visible machinery),
- engages with the existing decision records instead of ignoring them,
- describes behavior precisely enough that two people would implement the same thing,
- and states its own costs honestly — what gets slower, heavier or more complex because of it.

An RFC will be rejected, kindly, if it amounts to "add a settings panel", "embed Chromium and move on", or "just make it a plugin system". Those are the defaults Nebula exists to avoid; the reasoning is in the decision records.

## Style for documentation changes

Write plainly. Prefer short sentences and concrete examples over abstractions. Avoid marketing language anywhere in the docs — the project should sound like an engineering notebook, not a landing page. British or American spelling both fine; be consistent within a file.

## Licensing of contributions

All contributions are made under the [AGPL-3.0](LICENSE). Your first pull request must include the CLA sign-off described in [CLA.md](CLA.md). Pull requests without it can't be merged, no exceptions — it protects the project's ability to stay free.

## Conduct

Be direct about ideas and generous with people. Details in [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).
