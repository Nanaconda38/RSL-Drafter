<p align="center">
  <img src="rsl-drafter-brand-logo-transparent.png" alt="RSL Drafter orbital RD logo" width="220">
</p>

<h1 align="center">RSL Drafter</h1>

<p align="center">
  <a href="https://rsldrafter.app">Website</a> ·
  <a href="https://rsldrafter.app/beta-application">Apply for beta access</a> ·
  <a href="https://discord.gg/Nzm5487Ncz">Discord community</a>
</p>

RSL Drafter is a Windows bot for RAID: Shadow Legends Live Arena. It runs a Preset Lineup that you configure: five champion slots, ordered substitutes, draft priorities and supported Battle Openers. You review the setup and explicitly start each bounded session.

This repository provides product information and release downloads. The application source code is not published here.

## Public beta

- **Current build:** [RSL Drafter 0.1.3](https://github.com/Nanaconda38/RSL-Drafter/releases/tag/v0.1.3)
- **Platform:** Windows x64
- **Supported game build:** RAID: Shadow Legends 11.75.0
- **Download:** Public on the GitHub Releases page.
- **App access:** Applications are reviewed by a person. Verify your email, then wait for the decision. If approved, a single-use invitation lets you create an account. An approved account and access grant are required to use the app.

## What the bot does

1. **Build a five-champion lineup.** Choose a primary for each slot, order substitutes and link fallback slots.
2. **Set draft priorities.** Configure Pick Rules, ban priorities, leader order and supported Battle Openers.
3. **Run a bounded Live Arena session.** Review the plan and session limits, connect to RAID, then explicitly start.

The current public release supports **Preset Lineup for Live Arena only**. Adaptive Draft and Classic Arena are not available. RSL Drafter does not promise a win or choose a meta team for you.

## Download and get started

1. Download the Windows installer from the [v0.1.3 release page](https://github.com/Nanaconda38/RSL-Drafter/releases/tag/v0.1.3) and read its release notes.
2. [Apply for beta access](https://rsldrafter.app/beta-application) and verify your email.
3. If your application is approved, use the single-use invitation to choose a username and create your account.
4. Open RSL Drafter and choose **Sign in**. Enter your username or email and the password you set through the invitation.
5. Return to the app, launch RAID if needed, connect, review your Preset Lineup and session limits, then explicitly start Live Arena.

[Open the illustrated setup guide](https://rsldrafter.app/beta-get-started).

Never share your password or invitation link. Applying does not create an account or grant app access.

## Data and updates

The standard Release in public build 0.1.3 enables anonymous V3 and optional pseudonymous V1 match-sharing paths. Anonymous-only is the default choice, but collection starts only after you review and save the in-app notice. Account-linked sharing is a separate explicit choice bound to the signed-in beta account; the match JSON omits direct account identifiers. Turn sharing off in Settings to stop future collection/uploads and delete pending local records; records already accepted by the service are not removed by opting out.

Only complete, played Live Arena matches with an explicit Victory or Defeat are eligible. Match records contain observed draft facts, champion Base IDs, bans and leaders, result/quality categories, coarse points ranges (general points in 50-point buckets and seasonal points in 10-point buckets when available), and app/RAID/collector/catalog version metadata. They exclude player/opponent names and account IDs, device identifiers, exact points, roster/equipment inventories, screenshots, logs and raw game memory. See the [data notice](https://rsldrafter.app/data-notice) for the consent, field, network and retention details, and [TELEMETRY.md](TELEMETRY.md) for the technical inventory.

Pending match JSON is stored locally for up to 30 days, with a shared limit of 500 files or 50 MiB. The production service is configured to expire accepted raw match records after two calendar years; elapsed expiry has not been independently verified. An authenticated live-match receipt and retrieval have not yet been verified, so an upload attempt or local receipt counter is not proof of durable server storage.

The app checks GitHub for the latest release at every launch before sign-in. A newer version must be installed to continue; if the latest-version check is unavailable or invalid, sign-in remains blocked until the check can be retried. The app asks before downloading an update. GitHub receives the connection information needed for the startup check and any download; server-side logging and retention are not verified by RSL Drafter.

The public website does not use analytics. The beta application form uses Cloudflare Turnstile when applications are enabled. Account sign-in, beta access checks, applications, release checks and downloads are separate from match records.

## Reporting a problem

Before opening an issue, check the release notes and existing issues. Include:

- RSL Drafter version;
- RAID build;
- what you were doing;
- what you expected and what happened.

Do not post passwords, tokens, account details or unreviewed diagnostic files. Review any diagnostic report before attaching it.

[Open an issue](https://github.com/Nanaconda38/RSL-Drafter/issues)

## Disclaimer

RSL Drafter is an independent community project. It is not affiliated with, endorsed by or sponsored by Plarium. RAID: Shadow Legends and related marks belong to their respective owners.

## License

All rights reserved. See [LICENSE](LICENSE). No open-source license is granted for this repository or the application releases.
