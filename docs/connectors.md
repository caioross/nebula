# Connectors

Status: draft · Depends on: [architecture](architecture.md), [models](models.md)

## Position

A connector joins Nebula to something that talks back: a messaging service, a model provider, later perhaps calendars or mail. Connectors are core code — built, reviewed and shipped by the project, not a third-party plugin surface. The user's entire experience of a connector is sentences: establishing it, using it, repairing it, removing it. If any step requires leaving the canvas beyond what the external service itself legally forces (a QR code, an OAuth page), the design is wrong.

Model providers are specified in [models.md](models.md#connected-providers). This document covers messaging, and the connector contract generally.

## The connector contract

Every connector implements the same internal trait against the spine:

- **Establish** — from a sentence ("connect my WhatsApp") to a working session, with every step narrated in words on the canvas. State machines with retries; a connector that can't connect says why in a sentence, not with a code.
- **Receive** — inbound events normalized to a common shape (sender, conversation, body, attachments, timestamps) and published to the spine. The canvas decides presentation; connectors never draw.
- **Send** — outbound actions accepted only through the spine's confirmed-action path. Anything leaving the machine through a connector is externally visible by definition, so every send confirms first under the same discipline as page actions ([web-engine.md](web-engine.md#reading-without-obeying), layer 2); foreign content in the conversation only adds a second reason to.
- **Health** — a connector knows its own state (connected, degraded, expired) and reports transitions as plain sentences, at most once per transition. "Your WhatsApp session expired — say 'reconnect WhatsApp' when you want it back."
- **Remove** — erasure on request, complete: session keys, message cache, everything, journaled as erased.

Isolation: connectors are the architecture's leading candidate for out-of-process sandboxing (open question 2 in [architecture.md](architecture.md#open-questions)) since they speak to hostile networks with long-lived credentials.

## WhatsApp, first and honestly

WhatsApp is first because it is where the people Nebula is for actually live — in Brazil it *is* the phone. Incoming messages surface on the response canvas as quiet, dismissable lines ("Marcos: are we still on for tonight?"); replying is a sentence ("tell him yes, 8pm"), confirmed before sending, threaded correctly. A conversation can be pulled up whole ("show me the chat with Marcos") and the messages become context the models can work with ("summarize what the group decided about the trip") under foreign-content rules.

The honest part, recorded here so nobody discovers it in an issue later: **WhatsApp has no official API for personal accounts.** The realistic technical paths are the multi-device protocol as implemented by reverse-engineered libraries (the whatsmeow lineage), with real terms-of-service exposure and breakage risk, or the official Business API, which excludes personal accounts and changes the product's meaning. This is a *strategy decision, not just an engineering one*, and it is deliberately unresolved in this revision — it goes to [ADR-0005](decisions/0005-whatsapp-strategy.md) once the options are researched to the bottom, including the failure mode where WhatsApp actively blocks unofficial clients. What is decided: the connector contract above is designed so that WhatsApp is one implementation of it, and the first release milestone (M4) commits to *a* mainstream messenger, with WhatsApp the strong preference and a fallback candidate (Telegram, which has a real API) fully specified in the same ADR.

## Notification discipline

Connectors are where attention-stealing enters a product, so the rules are strict: incoming messages appear on the canvas without sound, without badges, without stealing focus, and without interrupting composition — if the user is mid-sentence, arrivals wait. "Don't show me messages while I'm working" is a preference the vault keeps. Nebula never becomes a notification center; it is a place where messages *can* surface, calmly, and where silence is a setting expressible in one sentence.

## Open questions

1. The WhatsApp strategy ADR (above), still the largest unresolved external-dependency risk in the project. It resolves at M4, where [ADR-0005](decisions/0005-whatsapp-strategy.md) is due before planning closes. The risk has a floor under it in the meantime: that ADR already specifies the Telegram fallback for the case where no WhatsApp path turns out to be responsible enough to ship.
2. Attachment handling: images and documents arriving through messages intersect with file handling and with foreign-content rules. M4, and it waits on a document rather than on evidence: the memory and file design it has to be written jointly with is not specified anywhere yet.
3. Whether conversation history from connectors enters long-term memory by default or only by request. Privacy instinct says by request; usefulness says default. M4, settled by watching the actual audience, and the rules do not shortcut it: founding constraint 3 governs what leaves the machine, while this is a question about what the machine takes in locally.
