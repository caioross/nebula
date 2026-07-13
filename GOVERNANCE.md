# Governance

Nebula uses a simple model, common to young open source projects: a single project lead with final say, supported by maintainers as the community grows. The structure is deliberately small; process should never outweigh the work.

## Roles

**Project lead.** The founder holds final decision authority over the product direction, the interface model, the acceptance of RFCs, releases and the project's name and identity. The lead's defining duty is to protect the founding constraints — the things that make Nebula what it is — against well-meaning erosion.

**Maintainers.** Contributors with a track record of high-quality, sustained work may be invited to maintainership. Maintainers triage issues, review pull requests, shepherd RFC discussions and merge changes within areas delegated to them. Maintainers are listed in this file as they are appointed.

**Contributors.** Anyone who opens a useful issue, improves a document, or authors an RFC. No formal status required.

## How decisions are made

Day-to-day matters are settled in issues and pull requests by whoever is maintaining that area. Anything that changes specified behavior goes through the RFC process described in [CONTRIBUTING.md](CONTRIBUTING.md). Accepted decisions of lasting consequence are recorded in [docs/decisions](docs/decisions); those records are binding until superseded by a new record, not by informal agreement.

When discussion stalls, the project lead decides. A decision made this way is documented with its reasoning, so it can be revisited with better arguments rather than relitigated with the same ones.

## The founding constraints

These are not up for majority vote. Changing any of them requires a decision record authored or explicitly endorsed by the project lead:

1. Language is the only control surface. No buttons, menus, panels or configuration files exposed to the user.
2. The product works completely on first launch, offline, with no account and no setup.
3. User data and conversation stay on the user's machine unless the user explicitly sends them elsewhere.
4. The core is built and owned by this project — not a wrapper around an existing browser or a thin client for one model provider.

## Changes to this document

By pull request, accepted by the project lead.
