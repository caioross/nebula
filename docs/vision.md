# Vision

Status: stable · Owner: project lead

## The claim

Software peaked in capability and collapsed in usability at the same time. A modern computer can translate languages, write contracts, read the entire web and reason about code — and most people can't reach any of it, because reaching it means navigating an archipelago of apps, accounts, settings, extensions and jargon. The interface is the bottleneck, not the intelligence.

Nebula's claim is that the interface can be reduced to a single act: writing. Not "chat" as a feature inside a program, but writing as the entire program. If that claim holds, whole categories of interface — menus, toolbars, settings screens, onboarding flows, plugin marketplaces — become unnecessary for the person using the machine. They don't disappear; they move inside, where software should have been carrying the burden all along.

## Where the idea comes from

We teach AI to working adults. Watch a classroom try to use today's tools and the failure pattern is always the same. Nobody struggles to say what they want. They struggle with everything around it: which model to pick, what an agent is, why the connector needs configuring, where that setting lives, what a token is and why it ran out. The industry's answer has been to explain harder — more onboarding, more tooltips, more courses like ours. Nebula's answer is that there should be nothing to explain.

Two products prove pieces of the thesis. WeChat proved that people will happily live inside one surface if it genuinely does everything. The current generation of AI coding agents proved that plain language can drive arbitrarily complex work. Nobody has put the two together for ordinary people, on their own machine, without an account, without a cloud dependency and without a manual.

## What Nebula is

A program you install on your computer. It opens as a clean surface — light or dark, the machine's choice until you say otherwise. Near the top sits a soft region we call the cloud: the one place you write. There is nothing else. No buttons anywhere, no close box, no menu bar, no gear icon, ever.

You write. Nebula reads what you wrote and decides what it is:

- **An address.** `github.com` or anything shaped like a destination — the page opens on the surface below, alive and usable.
- **An instruction.** "close", "make it dark", "use a stronger model", "connect my OpenAI key: sk-..." — done, confirmed in a sentence.
- **Work.** "summarize this page", "write a rent contract for my apartment", "answer Marcos on WhatsApp that I'll be late" — routed to the best engine for the job and delivered below, fading in as it arrives.

The response area has no border with the cloud. Content surfaces beneath your words and recedes when you move on. You can call anything back by asking for it. Closing the program is saying goodbye.

Everything ships in the box. Local models are bundled and chosen automatically by a small router that classifies each request before any large model wakes up. The machine is useful with no network at all. If you want a frontier model from a provider, you tell Nebula the key in plain words, once, and it becomes one more engine the router can pick.

Nebula lives in a single folder, like a browser does — models, memory, configuration, cache, all inside. But no user ever opens that folder. Its contents are Nebula's concern.

## Who it is for

The person who was never going to install Ollama. The retiree who types with two fingers and has a question about a letter from the bank. The student who wants the machine to just answer. The teacher preparing material at midnight. Power users are welcome — the ceiling is high, because language scales with the sophistication of the person writing — but the floor is what we defend. Every design review asks one question first: *does this add anything a first-time user must understand before writing?* If yes, it fails.

## What Nebula refuses to be

- **A browser with a sidebar.** The web is one of Nebula's abilities, not its frame.
- **A front-end for one AI company.** Models are interchangeable engines below decks. Loyalty is to the user.
- **A platform of visible extensions.** Capability grows in the core, through the project, where it stays coherent — not as a marketplace the user must curate.
- **A cloud service wearing a desktop costume.** What you write and what Nebula learns about you stay on your machine unless you explicitly send them somewhere.

## What success looks like

Someone's mother uses Nebula daily and, asked what it is, says "it's where I write what I need." She has never seen a setting. She doesn't know which model answered her, and has never needed to. When something didn't work, she told Nebula, and Nebula fixed it or said plainly that it couldn't.

That's the whole ambition. It is much harder than it sounds, which is why the rest of these documents exist.
