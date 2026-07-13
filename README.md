# Nebula

**One surface. You write, it happens.**

Nebula is a desktop program with no buttons, no menus, no settings screens and no manual. It opens as a quiet canvas with a single writing field. Type an address and the page opens below. Type a request and it gets done. Type "make it dark" and the theme changes. Type "bye" and it closes.

That's the whole interface.

> Nebula is in its **design phase**. There is no code to run yet, and that is deliberate. What exists today is the specification of the entire system, written before the first commit of code, so that every part is built once and built right. If you want to shape what this becomes, this is the best moment to arrive. See [Contributing](CONTRIBUTING.md).

## Why

Every capable program today asks something of you before it gives anything back. Install this, configure that, learn where the setting lives, understand what a plugin is, paste a key, restart. The people who most need computers to work for them are the ones these rituals exclude.

We teach people to use artificial intelligence for a living. The models were never the hard part. The hard part is everything wrapped around them: panels, connectors, agent frameworks, extension marketplaces. Users don't fail at asking for what they want. They fail at the machinery.

Nebula removes the machinery. If you can write a sentence, you can operate all of it.

## What it is

A single window. The top of the canvas holds the field — we call it the cloud — where everything is written. There is no border between what you write and what comes back; results surface below and fade in as they're ready. The conversation is the interface, the file manager, the browser bar and the settings panel at once.

Behind the canvas, three ideas carry the design:

**Language is the only control.** Anything you would click in another program, you say here. Configuration, connection, correction, closing. If a feature can't be reached through plain writing, the feature is wrong.

**Local first, complete on arrival.** Nebula ships with language models inside it. A small local router reads each request, decides what kind of work it is, and hands it to the best engine available — a bundled model for most things, a heavier one when depth is needed, or a provider you've connected by simply telling Nebula your key. Nothing to download separately, nothing to configure, no account required to start.

**The web as structure, not pictures.** When Nebula opens a page, it doesn't just paint pixels. It reads the page the way an engineer would — the document itself, its structure and meaning — so the same intelligence that answers your questions can see what you see and act on it precisely.

## What it is not

Nebula is not a browser with a chatbot bolted on, not an Electron shell around a website, and not another chat client for one company's model. It is built from the ground up as its own kind of program, in Rust, with its own rendering, its own routing and its own rules. The reasoning behind each of those choices is public, in [docs/decisions](docs/decisions).

## Documentation

| Document | What it covers |
|---|---|
| [Vision](docs/vision.md) | The product in full: who it's for, what it refuses to be |
| [Architecture](docs/architecture.md) | System layers, from canvas to model runtime |
| [Interface](docs/interface.md) | The cloud: behavior, motion, states, the no-button rule |
| [Routing](docs/routing.md) | How input becomes intent, and intent becomes action |
| [Models](docs/models.md) | Bundled models, swapping, connecting external providers |
| [Web engine](docs/web-engine.md) | Structural page reading and rendering strategy |
| [Connectors](docs/connectors.md) | Messaging and external services, WhatsApp first |
| [Roadmap](ROADMAP.md) | Phases and milestones |
| [Decisions](docs/decisions) | Records of the choices that define the project |

## Participating

The project runs on written proposals. Substantial ideas start as an RFC in [docs/rfcs](docs/rfcs); smaller things go through issues. Read [CONTRIBUTING.md](CONTRIBUTING.md) before opening either — it's short, and it explains the quality bar and the process. Governance, including how decisions get made and by whom, is described in [GOVERNANCE.md](GOVERNANCE.md).

## License

Nebula is free software under the [GNU AGPL-3.0](LICENSE). Contributions require a signed [Contributor License Agreement](CLA.md), which keeps the project able to defend itself and its name. The name "Nebula" is a working codename and may change before the first release.
