# TTAT Analytics — public OAuth information site

This is the public website for the **TTAT Analytics** OAuth application: the homepage and privacy policy that Google's OAuth consent screen links to. TTAT Analytics is a private, read-only analytics utility used by the operator of the Thirty Three And A Third YouTube channel. The application itself (TTAT Studio Tool 07) is **not** part of this repository.

- Production URL (after publication): https://analytics.lyfeisacircus.com/
- Homepage: `index.html` · Privacy Policy: `privacy/index.html` (served at `/privacy/`)
- Operator: Thirty Three And A Third · Contact: thirtythreeandathirdtv@gmail.com

## What this site is (and is not)

- Static HTML and CSS only. **No JavaScript, cookies, analytics, tracking, advertising, web fonts or third-party resources.**
- Every page carries a strict Content Security Policy (`default-src 'none'; style-src 'self'; img-src 'self'; base-uri 'none'; form-action 'none'`) as a `<meta>` tag, because GitHub Pages cannot set HTTP headers. (`frame-ancestors` cannot be set this way.)
- Outbound links (YouTube Terms of Service, Google Privacy Policy, Google Account permissions, OpenAI and GitHub privacy policies) are ordinary links; nothing is loaded from them.
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

## Before publishing

1. **Effective date.** Update the single marked line in `privacy/index.html` (search for `EFFECTIVE DATE`) to the actual publication date, including the `datetime` attribute.
2. Review both pages one final time against the current Tool 07 behavior.

## Preview locally

The Content Security Policy only allows files from the site's own origin, so preview through a local web server rather than opening files directly:

```bash
cd ~/Development/TTAT_Analytics_Web
python3 -m http.server 8765 --bind 127.0.0.1
# then open http://127.0.0.1:8765/
```

(The local server does not emulate GitHub's 404 handling; open `http://127.0.0.1:8765/404.html` to view that page.)

## Publishing with GitHub Pages (not yet done)

1. Create a GitHub repository and push this folder to its default branch.
2. Repository **Settings → Pages**: deploy from the default branch, folder `/ (root)`.
3. Custom domain `analytics.lyfeisacircus.com` (already in `CNAME`), then enable **Enforce HTTPS** once the certificate is issued.
4. DNS (GoDaddy): add a `CNAME` record for host `analytics` pointing to `<github-username>.github.io`. Consider verifying the domain in GitHub to prevent takeover.
5. In Google Cloud (OAuth branding), set the application home page to `https://analytics.lyfeisacircus.com/` and the privacy policy to `https://analytics.lyfeisacircus.com/privacy/`. Google may require the domain to be verified in Google Search Console and added as an authorized domain.

## Phase 3 prerequisites described by the privacy policy

The privacy policy describes how YouTube data **will** be handled once live collection is enabled. Tool 07 must implement and test all of the following before any live YouTube data is stored (YouTube API Services Developer Policies §III.A, §III.D.2, §III.E.4):

- [ ] `--revoke`: revoke the token with Google immediately, delete the Keychain item and delete stored YouTube data within 7 days.
- [ ] `--delete-data`: delete stored YouTube data on request within 7 days.
- [ ] Authorization re-check at least every 30 days; delete stored data within 30 days if authorization is revoked or cannot be renewed.
- [ ] 30-day refresh-or-delete for non-statistical YouTube data (titles, descriptions, channel name and handle).
- [ ] Check at least every 30 days for deleted videos and delete their stored data.
- [ ] Live custom date ranges use direct API queries; no calculated values stand in for YouTube metrics.
- [ ] Calculated figures are shown alongside their YouTube source values and labeled as calculated by TTAT Analytics.
- [ ] YouTube Terms of Service link and agreement statement, plus a link to this privacy policy, in Tool 07's sign-in, dashboard and reports.
- [ ] Live YouTube data stored only in `~/Library/Application Support/TTAT/Analytics/data/`, outside TTAT_STUDIO and its backups.
- [ ] Tool 07 analytics logs move from `TTAT_STUDIO/.ttat/logs` to the controlled local Application Support analytics area (`~/Library/Application Support/TTAT/Analytics/`) before live collection, because logs can contain YouTube channel IDs and other API-derived identifiers.
- [ ] Raw API records are immutable during their permitted lifetime and never silently overwritten; policy-required expiration and deletion are explicit, logged operations.

When live collection is enabled, update the privacy policy's "Current status" sections and its effective date.
