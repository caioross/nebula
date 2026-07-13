# 0005 — WhatsApp connector strategy

Status: open — research phase

## Context

WhatsApp is the first messaging target ([connectors.md](../connectors.md)) because it is where Nebula's audience lives, and it is the project's largest external-dependency risk: there is no official API for personal accounts.

## Paths under research

1. **Multi-device protocol via reverse-engineered implementations** (the whatsmeow lineage). Real user-account integration, the experience the product wants — with terms-of-service exposure for users, breakage whenever the protocol shifts, and the documented possibility of account bans for unofficial clients. The research must establish current ban rates, protocol stability history, and what a responsible disclosure to users looks like ("connecting WhatsApp this way is against their terms — here is the real risk — proceed?" in plain words).
2. **WhatsApp Business API.** Official and stable, but built for businesses messaging customers; it does not connect a person's own account, which changes the feature's meaning entirely. Likely useful only for a narrow notify-me channel, if at all.
3. **Companion-device bridging** (pairing as a linked device, as several bridge projects do). Technically a variant of path 1 with a different risk surface; evaluated together.

## The fallback, specified now

If no WhatsApp path is responsible enough to ship, M4's messenger commitment is met with **Telegram**, which offers a real client API. The connector contract is messenger-agnostic precisely so this substitution costs an implementation, not a redesign. The product preference remains WhatsApp; the project's integrity preference is not shipping a connector that gets its users banned without informed consent.

## Decision criteria

User risk (quantified, not rumored), maintenance burden per protocol change, and whether the connection experience can honor the one-sentence establishment promise. Decision due before M4 planning closes.
