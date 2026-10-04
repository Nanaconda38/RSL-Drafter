# RSL Drafter telemetry and data inventory

**Status checked:** 2026-10-04  
**Public client covered:** Windows beta 0.1.2, RAID build 11.75.0

This is a technical inventory of the current public beta and the related service contracts. It is not a promise that a later release behaves the same way; check that release's notes and in-app notice. “Anonymous” below describes the match payload, not every network or account record that can exist around a request.

## At a glance

- The public Windows 0.1.2 release does **not** upload match telemetry to an RSL Drafter server. Sign-in, beta access checks, applications and downloads are separate services.
- The public site says it uses no analytics. The application form uses Cloudflare Turnstile when applications are enabled.
- The API contract currently defines anonymous match ingestion (including a legacy format). Its source sets a 365-day retention period for accepted match records. This server capability does not mean the public 0.1.2 client sends those records.
- The public API contract does not implement pseudonymous match ingestion or pseudonymous-consent endpoints. No pseudonymous match history is currently collected by that API.
- A match payload that omits a name can still be distinctive. “Anonymous” is not a guarantee that a match can never be linked to someone, especially when combined with network metadata.

## Anonymous match data

### Current public behavior

The published 0.1.2 client does not send match records. The API source accepts anonymous schema versions V1 and V3 if a compatible, authorized client sends them. It validates and stores only accepted records; an API implementation detail or local development build should not be mistaken for proof that a particular public client is transmitting data.

### Fields in the anonymous API schemas

An anonymous V3 envelope can contain:

- Event and software metadata: a random event ID used to prevent duplicate inserts on retries; app version; RAID version; a SHA-256 fingerprint of the RAID build; collector version; and catalog version.
- Arena context: Live Arena mode, season, player and opponent divisions, and coarse player/opponent points or points-gap buckets where supplied.
- Draft summary: pick mode, which side picked first, draft phases/revisions, player/opponent sides, selected champions' public Base IDs, and whether order was known.
- Actions and result: each side's ban and leader Base IDs; winner/draw, termination classification, result evidence, and data-quality classification/reasons.

The older V1 format accepted by the API can additionally contain detailed draft-decision records (including the selected Base IDs, decision source, fallback flag and failure code), a tier bucket, duration bucket, termination reason and a Battle Opener summary. This legacy schema is not the public 0.1.2 client's current upload behavior.

The server rejects prohibited identity properties such as email, username, player/opponent names and IDs, device or hardware IDs, exact rating, tokens and champion-instance IDs. These schemas describe selected champions in a match; they are not a full roster, equipment inventory, screenshot, or raw game-memory dump.

### Transport metadata and the limits of “anonymous”

If anonymous ingestion is used, the client enrolls with a random per-installation UUID and a random bearer key. The API stores the UUID and only a SHA-256 hash of the key; the key itself is not returned by the API. These values are used for authorization, rate limits and revocation, not included in the match envelope. The API uses the source IP for rate limiting, and the hosting/proxy provider can observe connection metadata. The match-record table does not have a source-IP field, but provider and service access-log retention has not been independently verified.

For these reasons, the anonymous match body is designed to omit direct account identifiers, but the whole network transaction should not be described as unconditionally anonymous.

### Retention

The API source configures accepted anonymous match records to expire after 365 days; a background retention pass runs daily. A received record includes the envelope plus event/schema/version/quality metadata, receipt time and payload size. This is the active-database retention rule in the checked API source, not an independently verified count or inspection of production records.

Daily PostgreSQL backups are kept in restricted local server storage for up to two years according to the public data notice. Deleting an active database record does not immediately remove it from an existing backup; it can remain there until that backup expires.

## Pseudonymous data

### Pseudonymous match sharing is not active

The current public API contract has no pseudonymous match or pseudonymous-consent route. Therefore, it does not currently accept or store a pseudonymous match history, and no server retention period applies to that category.

A pseudonymous match design would be different from anonymous sharing: the service could associate the same match facts with an authenticated beta account through an opaque identity-provider subject. The raw username or email need not be inside the match payload, but the service could still link the record back to the account. That would be pseudonymous, not anonymous. Any such feature must remain off until the route, separate consent, retention/deletion behavior and public notice are verified and documented.

### Other pseudonymous service identifiers

Separate from match telemetry, the beta service uses opaque identity references to check access grants. The grant database stores an issuer/subject reference and grant state, not the email or password. Administrative audit entries include the action, time and a one-way hash of the administrator identity; the audit-retention period has not been set or verified. These records support account access and administration; they are not match telemetry.

## Applications and accounts

The public beta application form collects an email address, Windows/RAID compatibility confirmation, selected Live Arena league, consent/notice version, and a Cloudflare Turnstile challenge. It does not request a first or last name, RAID username/player ID, password, champion roster or match data. The chosen account username is the applicant's pseudonym.

Before email verification, submitted fields are held temporarily; unverified requests expire and are deleted. After verification, the service stores the verified email, eligibility confirmation, league, consent/submission/expiry times and application status. Applications are deleted from the active database after 90 days. A human reviews applications. An approved applicant receives a single-use invitation that expires after seven days.

Keycloak, the identity provider, stores the username and password verifier. The site and application API do not handle the user's plaintext password. The account is linked to the approved application and access grant using an opaque issuer/subject reference. Email delivery providers receive the recipient address and message needed to send verification, decision, invitation or password-setup mail.

Authorized administrators can view the verified-email snapshot, league and status for an application, and current account email/enabled/verified state from the identity service. Grant records themselves store no email. User-confirmed account deletion revokes beta access and requests identity deletion; a short-lived safeguard may remain while identity deletion is retried. Database backups may retain deleted database data until backup expiry, as described above.

## Local files, diagnostics and support

Strategy and profile files stay on the user's computer. The champion reference catalog is bundled with the application and is not fetched from HellHades at runtime. The public update path is a manual GitHub Releases download; the in-app update feed is not configured for public beta 0.1.2.

Diagnostic reports are not uploaded automatically. If a user chooses to attach a report to GitHub or a Discord support ticket, the report and any accompanying text, logs or screenshots are shared with that service and the people who can access the issue or ticket. Review and redact every attachment first; do not include passwords, invitation links, tokens, RAID account details or unreviewed game screenshots.

When a Discord ticket is opened, Discord and the Ticket Tool integration process the Discord account identifier, username, ticket channel metadata and the messages or attachments submitted in that ticket. Server staff can read the private ticket. Their service-side log/retention behavior has not been independently verified by RSL Drafter.

## Website, hosting and third-party service metadata

The public site says it does not use analytics. The application page loads Cloudflare Turnstile and submits eligible applications to the separate API. The API uses the Cloudflare-provided client address for transient in-memory rate limits and does not put a source IP in the application record. Cloudflare, the hosting provider, API and email providers may process ordinary connection or delivery metadata; their service-side logging and retention have not been independently verified here.

GitHub receives the connection information needed to serve downloads. GitHub issues are public: anything posted there, including the account name, issue text and attachments, can be seen publicly. Discord and Ticket Tool are separate services and are subject to their own policies.

## Links and status boundary

- [Public beta data notice](https://rsldrafter.app/data-notice)
- [Current public release](https://github.com/Nanaconda38/RSL-Drafter/releases/tag/v0.1.2)
- [Report an issue](https://github.com/Nanaconda38/RSL-Drafter/issues)
- [Discord community](https://discord.gg/Nzm5487Ncz)

This inventory is technical documentation, not a complete legal privacy policy. It reflects the published 0.1.2 notice and API contracts checked on the date above. Before enabling a new telemetry category or changing the distributed build, verify the exact Distribution artifact and deployed service, then update this file and the website data notice together.
