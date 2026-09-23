# KICKOFF NAM — case study

An independent Namibian football archive designed for public discovery, with a separate private owner dashboard and invitation-based tester workspace.

**Status:** public archive live at [kickoff-nam.pages.dev](https://kickoff-nam.pages.dev/). Brand artwork refreshed 23 September 2026; the recorded archive scope was reviewed 12 September 2026. Private Access identity and tester acceptance remain gated. This case study is not a claim that the football coverage is complete.

<p align="center">
  <img src="assets/kickoff-nam.png" alt="KICKOFF NAM — Namibia Football Archive brand artwork" width="100%">
</p>

## The problem

Namibian football information is spread across dated squad announcements, governing-body pages, club directories and historical results. A useful discovery tool needs to preserve where a fact came from and when it was reported. Missing information should not become an invented current contract, market value or player-agent relationship.

## The product

- Searchable profiles for 103 players, including 43 provisional youth selections and 23 Brave Gladiators squad records.
- 28 club-directory entries, all 14 regional associations, competition pathways, coaching resources and selected match archives.
- 22 NFA-sourced club marks with source-aware attribution; six clubs retain explicit initials placeholders where a confident official match was unavailable.
- Five attributed archival photographs, each retaining its original source, author and reuse terms.
- Historical affiliation notes and source links rather than unsupported current-club assertions.
- Mobile controls, keyboard-accessible profiles, CSV export and explicit coverage limitations.

These counts describe collected archive entries. They do not establish complete national coverage, active registrations or live data feeds.

### Archive context

<p align="center">
  <img src="https://thumb.wikimedia.org/wikipedia/commons/thumb/7/7a/SamNujomaStadium.JPG/1200px-SamNujomaStadium.JPG" alt="Sam Nujoma Stadium in Windhoek" width="100%">
</p>

<p align="center"><sub>Sam Nujoma Stadium, Windhoek · <a href="https://commons.wikimedia.org/wiki/File:SamNujomaStadium.JPG">original photograph and attribution</a></sub></p>

## Public browsing, private administration

| Surface | Access | Purpose |
| --- | --- | --- |
| Football archive | Public; no sign-in | Explore football records and sources |
| Owner dashboard | Verified owner identity | Invite/revoke testers, review feedback, inspect activity |
| Tester workspace | Verified identity plus an accepted invite and active session | Submit bugs, data corrections and suggestions |

The private backend uses Cloudflare Access for identity verification, a Pages Worker for authorization, and a dedicated D1 database for invitations, memberships, sessions, feedback and audit events. Owner and tester permissions are checked on the server, not inferred from visible UI controls.

## Security decisions

- Individual email-bound invitations, with limited validity and a single successful redemption.
- Cryptographically random tokens stored as hashes; invitation codes travel in URL fragments rather than request URLs.
- Secure, HttpOnly, SameSite session cookies bound to the verified email and membership grant.
- Revocation removes application sessions and pending invites; old session cookies remain invalid after a later re-invitation.
- Transactional redemption prevents duplicate sessions from simultaneous invite use.
- Signed identity validation includes issuer, audience and expiry; an email header alone is never trusted.
- No-store private responses, anti-framing headers, same-origin mutation checks, bounded inputs, rate limits and parameterized SQL.
- Dashboard text is rendered as text, not executable HTML. Platform error logs omit credentials and feedback contents.

Invitations are manually copied and shared privately. Automatic invitation-email delivery is not implemented. Revocation blocks subsequent requests, not information a tester has already downloaded or viewed.

## Verification evidence

21 backend regression tests pass locally. They use real signed JWT fixtures and SQLite running the migration and production SQL. They cover authentication forgery, wrong identity audience, owner-only access, CSRF, token storage, concurrent redemption, expiry, cancellation, revocation, logout, feedback authorization, rate limits and transactional rollback.

The public registry regression suite and production bundle build pass. The dependency audit reported no known vulnerabilities at the recorded check; that is not a guarantee of overall security.

In-app checks verified the owner dashboard without horizontal document overflow at six widths from 320 to 1280 CSS pixels. Phone checks also covered 48px admin controls, invitation/feedback layouts at 320px, public filter sizing at 390px, player search, dialog opening and focus restoration after Escape. A native mobile section picker makes all eight sections reachable without hunting through horizontally hidden tabs; 32 section/width checks passed across 320, 360, 390 and 540px. The private UI preview used synthetic data.

The full standalone browser regression suite remains incomplete because the host could not start its test browser. Local JWT/SQLite tests are not a substitute for live Cloudflare Access, Workers and D1 acceptance tests.

## Next release gates

Configure the actual Cloudflare Access identity application and Pages-to-D1 binding, complete owner sign-in and private-route acceptance, finish browser failure/zoom checks, agree tester-data retention, and complete owner MFA enrollment before broad tester onboarding. The public URL is already verified; no tester credentials have been issued.

## Reusable lesson

Keep public content public and put administrative power behind server-enforced ownership. Give each tester a limited invitation with revocation, and make the boundary visible in the product. Reuse this pattern for future projects only after adapting its identity, roles, data sensitivity and retention requirements.

## Scope of this repository

This repository is documentation only. It does not contain private application source, account credentials, real tester identities, invitation tokens or a database export. Photograph licences belong to their respective authors; this case study does not relicense those assets.
