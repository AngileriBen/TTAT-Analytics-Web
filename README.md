# TTAT Analytics — public OAuth information site

This is the public website for the **TTAT Analytics** OAuth application: the homepage and privacy policy that Google's OAuth consent screen links to. TTAT Analytics is a private, read-only analytics utility used by the operator of the Thirty Three And A Third YouTube channel. The application itself (TTAT Studio Tool 07) is **not** part of this repository.

- Production URL: https://analytics.lyfeisacircus.com/
- Homepage: `index.html` · Privacy Policy: `privacy/index.html` (served at `/privacy/`)
- Operator: Thirty Three And A Third · Contact: thirtythreeandathirdtv@gmail.com

## Implementation status (October 2, 2026)

- **OAuth sign-in (Tool 07):** working. Read-only scopes only (`youtube.readonly`, `yt-analytics.readonly`); on the Mac the refresh token is kept in the macOS Keychain.
- **Live collection:** working, for the Thirty Three And A Third channel only.
- **Storage:** active, on the operator's Mac in TTAT Analytics' Application Support area, outside the shared TTAT production storage and its backups. New logs are kept there as well. A copy may also be kept in a private Google Cloud Storage bucket for GitHub Actions jobs (see the Privacy Policy).
- **Compliance lifecycle controls:** implemented (sign-out with revocation, data deletion, authorization re-checks, 30-day metadata expiry, removed-video purge). They run whenever Tool 07 runs, on the Mac or in a GitHub Actions job.
- **Automated daily cloud job and emailed briefing:** described in the Privacy Policy (separate "TTAT Analytics Cloud" OAuth client with its credentials in Google Secret Manager; briefing sent through Resend from `reports@analytics.lyfeisacircus.com`). Being implemented; not yet turned on.
- **This website:** informational and privacy-policy infrastructure only. It never contains or displays channel analytics.
- **Dashboard:** separate work, not yet complete.

## What this site is (and is not)

- Static HTML and CSS only. **No JavaScript, cookies, analytics, tracking, advertising, web fonts or third-party resources.**
- Every page carries a strict Content Security Policy (`default-src 'none'; style-src 'self'; img-src 'self'; base-uri 'none'; form-action 'none'`) as a `<meta>` tag, because GitHub Pages cannot set HTTP headers. (`frame-ancestors` cannot be set this way.)
- Outbound links (YouTube Terms of Service, Google Privacy Policy, Google Account permissions, OpenAI, GitHub and Resend privacy documents) are ordinary links; nothing is loaded from them.
- It contains no secrets. Never add OAuth client files, tokens or credentials to this repository; `.gitignore` blocks the common file names as a safeguard.

## Files

```
index.html            Homepage
privacy/index.html    Privacy Policy
404.html              GitHub Pages "not found" page (uses root-absolute paths on purpose)
assets/styles.css     Site styling (TTAT Brand Bible palette, system fonts)
assets/favicon.svg    Site icon
CNAME                 Custom domain for GitHub Pages: analytics.lyfeisacircus.com
.nojekyll             Serve files as-is (no Jekyll processing)
.gitignore            Keeps macOS, editor and credential-like files out of Git
```

## Updating the policy

1. **Dates.** Keep the effective date. When the policy changes, update "Last updated" in `privacy/index.html` (search for `DATES`), including its `datetime` attribute.
2. Review both pages against the current Tool 07 behavior before every publication.

## Preview locally

The Content Security Policy only allows files from the site's own origin, so preview through a local web server rather than opening files directly:

```bash
cd ~/Development/TTAT_Analytics_Web
python3 -m http.server 8765 --bind 127.0.0.1
# then open http://127.0.0.1:8765/
```

(The local server does not emulate GitHub's 404 handling; open `http://127.0.0.1:8765/404.html` to view that page.)

## Publishing with GitHub Pages

1. The repository is on GitHub (`origin`); publish from its default branch.
2. Repository **Settings → Pages**: deploy from the default branch, folder `/ (root)`.
3. Custom domain `analytics.lyfeisacircus.com` (already in `CNAME`), then enable **Enforce HTTPS** once the certificate is issued.
4. DNS (GoDaddy): add a `CNAME` record for host `analytics` pointing to `<github-username>.github.io`. Consider verifying the domain in GitHub to prevent takeover.
5. In Google Cloud (OAuth branding), set the application home page to `https://analytics.lyfeisacircus.com/` and the privacy policy to `https://analytics.lyfeisacircus.com/privacy/`. Google may require the domain to be verified in Google Search Console and added as an authorized domain.

## Phase 3 compliance requirements described by the privacy policy

Tool 07 had to implement and test all of the following before any live YouTube data was stored (YouTube API Services Developer Policies §III.A, §III.D.2, §III.E.4). All were implemented and tested before the first live collection on September 29, 2026:

- [x] `--revoke`: revoke the token with Google, delete the Keychain item and delete stored YouTube data (immediately; a revocation Google does not confirm needs the operator's confirmation before local removal).
- [x] `--delete-data`: delete stored YouTube data on request (immediately).
- [x] Authorization re-check on every collection and `--check`; if sign-in stops working, stored data is deleted at the first run at least 7 days later unless access is restored.
- [x] 30-day refresh-or-delete for non-statistical YouTube data (titles, publish times, durations, channel name and handle). Descriptions and tags are never collected.
- [x] Removed-video check on every collection, deleting stored data for removed videos.
- [x] Live custom date ranges use direct API queries; no calculated values stand in for YouTube metrics.
- [x] Calculated figures are labeled as calculated by TTAT Analytics.
- [x] YouTube Terms of Service agreement statement and privacy-policy link at sign-in, on every collection and in collection summaries. (Dashboard: to be added with the dashboard.)
- [x] Live YouTube data stored locally in `~/Library/Application Support/TTAT/Analytics/data/`, outside TTAT_STUDIO and its backups; any cloud copy is kept only in a private Google Cloud Storage bucket, under the same rules (see the Privacy Policy).
- [x] New Tool 07 analytics logs written to `~/Library/Application Support/TTAT/Analytics/logs/`, because logs can contain YouTube channel IDs.
- [x] Raw API records are immutable during their permitted lifetime and never silently overwritten; policy-required expiration and deletion are explicit, logged operations.

**Operational commitment:** Tool 07 applies these rules only when it runs (on the Mac, in an operator-started job or, once enabled, in the daily scheduled job). The policy therefore commits the operator to running TTAT Analytics (or deleting the stored data) at least every 30 days while YouTube data is stored, and to ensuring deletion within 30 days of any revocation, including deleting the cloud job's refresh token from Google Secret Manager when the cloud copy must be deleted.
