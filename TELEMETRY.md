# RSL Drafter telemetry and data inventory

**Status checked:** 2026-10-12  
**Public client covered:** Windows beta 0.1.3 standard Release  
**In-app notice:** 2026-10-v10

This is a technical inventory of the current public beta and the related service contracts. It is not a promise that a later release behaves the same way; check that release's notes and in-app notice. “Anonymous” below describes the match payload, not every network or account record that can exist around a request.

## At a glance

- The standard Release in public build 0.1.3 enables both anonymous V3 and optional pseudonymous V1 match-sharing paths to https://api.rsldrafter.app/.
- Anonymous-only is the default choice, but no match is collected until the user reviews and saves the in-app notice. Account-linked pseudonymous sharing is separate, default-off, and requires an explicit choice with the signed-in beta account.
- Only a complete, played Live Arena match with an explicit Victory or Defeat is eligible. Draws, pre-combat departures, unknown outcomes, abandoned matches and incomplete captures are not queued.
- Turning sharing off stops further collection and uploads, cancels an active request, and deletes pending local match files. It does not erase records already accepted by the service.
- Production route and retention configuration are present, but no authenticated real-player match has yet been independently verified as accepted and retrieved from production. Elapsed expiry is also unverified.
- The public site uses no analytics. The application sends a mandatory GitHub latest-release check at every launch before sign-in; GitHub receives ordinary connection metadata. The beta request form uses Cloudflare Turnstile when applications are enabled.
- “Anonymous” describes the match body only. Separate installation credentials and infrastructure connection metadata mean the whole network transaction should not be described as unconditionally anonymous.

## Anonymous match data

### Current public behavior

The standard Release enables anonymous V3 match delivery. An eligible match is saved to the local outbox only after the in-app notice is saved and anonymous sharing remains selected. The uploader may send it to the fixed production API over HTTPS. A matching event-ID acknowledgment is counted by the app, but is not an independent check of database persistence. The public service has rejected unauthenticated probes; no authenticated live match has yet been accepted and retrieved for end-to-end verification.

### Fields in the current anonymous V3 schema

The current envelope is arena.match.anonymous.v3. It includes:

- **Event/software metadata:** a random per-match event ID for retry deduplication; app and RAID versions; a SHA-256 RAID-build fingerprint; collector version; and catalog version.
- **Arena context:** Live Arena mode; optional season/division fields; general player/opponent points in 50-point buckets; seasonal points in 10-point buckets when available; and a coarse points-gap bucket. Unavailable values can be null; exact point values are not sent.
- **Observed draft:** pick mode; which side picked first; pick batches with observed revision, phase, side, champion Base IDs and whether order is known; and both sides' observed bans and leaders.
- **Result and quality:** explicit Victory or Defeat; termination and evidence categories; and bounded quality classification/reasons.

The payload is fact-only: it does not include player or opponent names, RAID/Plarium account IDs, beta usernames/emails, local profile names, device/hardware IDs, tokens, exact match-start timestamps or exact ratings, full roster/champion-instance IDs, equipment, screenshots, logs, raw game memory, or bot decision rationale/source/fallback details. A champion Base ID is a public champion identifier, not a unique account roster-instance identifier.

The current client does not migrate or upload legacy anonymous V1/V2 queue files. It does not collect button-click or unrelated product-usage analytics, and automatic crash/error reporting is not enabled.

## Pseudonymous data

### Account-linked match sharing

The current standard Release also supports the separate arena.match.pseudonymous.v1 route. The match body has the same fact-only match fields as anonymous V3 and contains no username, email, beta-account ID, session token or contributor_id. The service derives the association from the authenticated beta-account session and stores that association separately from the match JSON. This makes the records pseudonymous, not anonymous: the service can link them to the account.

The user must explicitly choose account-linked sharing. The choice is bound locally to the signed-in beta account, protected with Windows DPAPI, and rechecked when a record is queued and uploaded. Changing accounts cannot send another account's pending linked records. Withdrawing the choice stops future linked sharing and removes pending linked files; it does not delete accepted server rows. Account-deletion handling for linked match rows has not been verified end to end.

### Installation credential and connection metadata

Anonymous sharing uses a random per-installation UUID and bearer secret, separate from the match body. The API stores the UUID and a SHA-256 hash of the secret; the client protects the secret with DPAPI. These values support authorization, quotas and revocation. The API and Cloudflare proxy may process the connection source IP to deliver and protect requests. The match-record schema has no source-IP field, but provider and service access-log retention has not been independently verified.

For these reasons, “anonymous” describes the body of an anonymous match record, not the surrounding request or every service-side security record.

### Retention and deletion boundary

The local outbox expires pending files after 30 days and is bounded to 500 files / 50 MiB across schemas. Production is configured to expire accepted anonymous and pseudonymous raw match rows after two calendar years. The configured period is not evidence that expiry has elapsed or been observed. Turning sharing off clears pending local records but does not erase accepted records. Backup retention and account-deletion purge of linked match rows have not been independently verified, so no immediate remote-deletion guarantee is made.

### Other pseudonymous service identifiers

Separate from match sharing, the beta service uses opaque identity references to check access grants. The grant database stores an issuer/subject reference and grant state, not the email or password. Administrative audit entries include the action, time and a one-way hash of the administrator identity; the audit-retention period has not been set or verified. These records support account access and administration; they are not match telemetry.

## Applications and accounts

The public beta application form collects an email address, Windows/RAID compatibility confirmation, selected Live Arena league, consent/notice version, and a Cloudflare Turnstile challenge. It does not request a first or last name, RAID username/player ID, password, champion roster or match data. The chosen account username is the applicant's pseudonym.

Before email verification, submitted fields are held temporarily; unverified requests expire and are deleted. After verification, the service stores the verified email, eligibility confirmation, league, consent/submission/expiry times and application status. Applications are deleted from the active database after 90 days. A human reviews applications. An approved applicant receives a single-use invitation that expires after seven days.

Keycloak, the identity provider, stores the username and password verifier. The site and application API do not handle the user's plaintext password. The account is linked to the approved application and access grant using an opaque issuer/subject reference. Email delivery providers receive the recipient address and message needed to send verification, decision, invitation or password-setup mail.

Authorized administrators can view the verified-email snapshot, league and status for an application, and current account email/enabled/verified state from the identity service. Grant records themselves store no email. User-confirmed account deletion revokes beta access and requests identity deletion; a short-lived safeguard may remain while identity deletion is retried. Database backups may retain deleted database data until backup expiry, as described above.

## Local files, diagnostics and support

Strategy and profile files stay on the user's computer. The champion reference catalog is bundled with the application and is not fetched from HellHades at runtime. At every launch, the public 0.1.3 client checks GitHub's latest release before sign-in; newer versions must be installed, and an unavailable or invalid check keeps sign-in blocked until retry. The app asks before downloading an update. GitHub receives ordinary request/download connection metadata; RSL Drafter has not verified GitHub's server-side logging or retention.

Diagnostic reports are not uploaded automatically. If a user chooses to attach a report to GitHub or a Discord support ticket, the report and any accompanying text, logs or screenshots are shared with that service and the people who can access the issue or ticket. Review and redact every attachment first; do not include passwords, invitation links, tokens, RAID account details or unreviewed game screenshots.

When a Discord ticket is opened, Discord and the Ticket Tool integration process the Discord account identifier, username, ticket channel metadata and the messages or attachments submitted in that ticket. Server staff can read the private ticket. Their service-side log/retention behavior has not been independently verified by RSL Drafter.

## Website, hosting and third-party service metadata

The public site says it does not use analytics. The application page loads Cloudflare Turnstile and submits eligible applications to the separate API. The API uses the Cloudflare-provided client address for transient in-memory rate limits and does not put a source IP in the application record. Cloudflare, the hosting provider, API and email providers may process ordinary connection or delivery metadata; their service-side logging and retention have not been independently verified here.

GitHub receives the connection information needed to serve downloads. GitHub issues are public: anything posted there, including the account name, issue text and attachments, can be seen publicly. Discord and Ticket Tool are separate services and are subject to their own policies.

## Links and status boundary

- [Public beta data notice](https://rsldrafter.app/data-notice)
- [Current public release](https://github.com/Nanaconda38/RSL-Drafter/releases/tag/v0.1.3)
- [Report an issue](https://github.com/Nanaconda38/RSL-Drafter/issues)
- [Discord community](https://discord.gg/Nzm5487Ncz)

This inventory is technical documentation, not a complete legal privacy policy. It reflects the public 0.1.3 standard Release profile and the 2026-10-v10 in-app notice checked on 2026-10-12. The standard Release source enables both upload routes, and production route/expiry configuration is documented; however, authenticated real-match receipt/retrieval, durable database storage, elapsed expiry, backup expiry and linked-row account-deletion behavior have not been independently verified end to end. Review the exact Distribution artifact and deployed service before making stronger claims.
