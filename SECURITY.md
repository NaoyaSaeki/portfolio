# Security Policy

This repository hosts a static portfolio website (HTML/CSS/JavaScript) served
via GitHub Pages. It has no backend, no database, and collects no user data.

## Supported Versions

Only the currently deployed version of the site (the `main` branch) is
supported. There are no released versions or branches to back-port fixes to.

## Reporting a Vulnerability

If you discover a security issue, please report it privately — **do not open a
public issue** for anything sensitive.

- **Preferred:** open a private report via GitHub Security Advisories
  ("Security" tab → "Report a vulnerability").
- **Email:** naoyasaeki@eng-jpn-service.com

Please include:

- a description of the issue and its potential impact,
- steps to reproduce (URL, browser, and any payload),
- any suggested remediation if you have one.

### Response targets

- **Acknowledgement:** within 5 business days.
- **Assessment & triage:** within 10 business days.
- **Fix / mitigation:** as quickly as is practical for a static site.

Please act in good faith: give us reasonable time to remediate before any public
disclosure, and avoid privacy violations, service disruption, or data
destruction while testing.

## Scope

In scope:

- The site source in this repository and its GitHub Pages deployment.
- Content-Security-Policy and other client-side protections.
- The GitHub Actions workflows in `.github/workflows/`.

Out of scope:

- Third-party services (e.g. Google Fonts, LinkedIn) — report to those vendors.
- Findings that require a compromised end-user device or browser.

## Hardening implemented in this repository

- **Content-Security-Policy** delivered via `<meta http-equiv>` on every page,
  built from the site's actual resources: `default-src 'self'` with no
  `unsafe-inline` / `unsafe-eval`, `object-src 'none'`, `base-uri 'self'`,
  `frame-ancestors 'none'`, `form-action 'self'`, and `upgrade-insecure-requests`.
- **Referrer-Policy** via meta (`strict-origin-when-cross-origin`).
- A ready-to-use [`_headers`](./_headers) file with full HTTP response headers
  (HSTS, X-Frame-Options, X-Content-Type-Options, COOP/CORP, Permissions-Policy)
  for hosts that support them (Netlify / Cloudflare Pages / reverse proxy).
- **CI security automation:** CodeQL analysis, gitleaks secret scanning, and a
  link check — all with least-privilege `permissions` and actions pinned to
  full commit SHAs.
- **Dependabot** for the `github-actions` ecosystem.

## Known limitations

- **GitHub Pages cannot send custom HTTP response headers.** Header-only
  protections (HSTS, X-Frame-Options, and the CSP `frame-ancestors` directive)
  are therefore **not enforced** on the Pages deployment. To enforce them, serve
  the site through a host/proxy that honours the [`_headers`](./_headers) file
  (Netlify, Cloudflare Pages, or an nginx/Cloudflare reverse proxy).
- **Google Fonts and Subresource Integrity (SRI).** The `fonts.googleapis.com`
  `css2` endpoint returns different CSS per browser (it `Vary`s on the request),
  so a fixed `integrity` hash would break rendering for some user agents. SRI is
  therefore intentionally omitted for that stylesheet; the request is instead
  constrained by the CSP. To obtain SRI and remove the third-party dependency
  entirely, self-host the font files and reference them from `'self'`.
