# 0002 — License: AGPL-3.0 with contributor agreement

Status: accepted

## Context

The project wants three things that pull against each other: a genuine open source community with the freedoms that attract serious contributors; protection against the commodity fate — someone lifting the work into a closed product or a hosted service and giving nothing back; and preserved ability for the founding project to make licensing moves later (dual licensing, defending the name) without chasing permissions from hundreds of past contributors.

## Options considered

**Permissive (MIT, Apache-2.0).** Maximum adoption, maximum vulnerability: any company can fork, close, and outspend the origin. For infrastructure libraries that trade is often right; for an end-user product whose value concentrates in one binary, it hands the endgame to whoever has the biggest marketing budget.

**Source-available (BSL, FSL, SSPL).** Strong commercial protection, but they are not open source by the accepted definition, and that word matters here: it decides whether the contributors Nebula needs will come, whether Linux distributions can carry it, and how the project is received. Rejected while the project's strategy is community-first.

**Copyleft (GPL-3.0).** Honest protection for distributed software, but silent on the network loophole — a company could run a modified Nebula as a hosted service and share nothing.

**AGPL-3.0.** Copyleft that closes the network loophole: anyone distributing or *serving* modified Nebula must publish their changes. Fully open source, OSI-approved, increasingly the choice of projects in exactly Nebula's position. Its reputation for scaring enterprises away is, for an end-user product, closer to a feature than a bug.

## Decision

AGPL-3.0 for all code. A contributor license agreement ([CLA.md](../../CLA.md)) on all contributions, granting the project relicensing ability while guaranteeing contributions remain permanently available under their submitted license. The project name and any future marks stay outside the code license.

## Costs accepted

Some contributors decline to sign CLAs on principle; the project accepts losing them, and mitigates with a CLA that guarantees the code stays free (clause 2). Some companies won't touch AGPL code; they are not the audience. Dual-licensing revenue, if ever pursued, requires keeping CLA records clean from the first pull request — which is why the CLA exists from day zero rather than being retrofitted.
