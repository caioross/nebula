# Security Policy

Nebula has no released code yet; this policy covers the specifications and will govern the codebase from its first commit.

## Reporting a vulnerability

Report security issues privately through GitHub's security advisory feature ("Report a vulnerability" on the Security tab) rather than in a public issue. You will get an acknowledgment within a week. Please include enough detail to reproduce the problem and, if you have one, a suggested fix.

Public disclosure is coordinated: once a fix ships, the report and credit to the reporter are published. If a report is not acknowledged or is handled poorly, the reporter is free to disclose after 90 days.

## Design-phase reports

Security review of the specifications themselves is welcome and does not need to be private. If you spot a flaw in the design — a prompt injection path through the web engine, a trust boundary the routing spec fails to draw, a way the connector model could leak messages — open an issue with the `security` label or, if you believe the flaw is exploitable enough that publishing it would be irresponsible even pre-code, use the private channel above.

## Scope of concern

The threat model that the architecture must answer to, in order of priority:

1. **Injection through content.** Pages, messages and documents that Nebula reads are untrusted input to models that can act. The boundary between "content the model reads" and "instructions the model follows" is the single most security-critical design problem in the project.
2. **Local data exposure.** Everything Nebula knows about its user lives in its folder. That folder's contents must be protected against exfiltration by malicious content, by connected services and by other local software to the extent the platform allows.
3. **Credential handling.** Keys for connected providers are entered as plain conversation. How they are recognized, stored, masked in history and excluded from model context is specified in [docs/models.md](docs/models.md) and is a standing audit target.
4. **Supply chain.** Bundled model weights and any third-party components are pinned and checksummed; releases are reproducible to the extent the toolchain allows.
