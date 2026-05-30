# Stubgroup cURL Website Screening Cheatsheet

A practical, developer-focused guide for validating website behavior at the HTTP layer using `curl`.

This repository is designed for technical screening work in marketing and growth contexts, where you need fast answers about redirects, bot handling, localization behavior, cookie/session impacts, and API behavior.

## What this repository contains

- **`curl-website-screening-cheatsheet.md`**: the full instructional guide with commands, workflows, and references.

## Intended audience

This guide is written for:

- Developers supporting SEO, analytics, paid-media landing pages, and localization rollouts
- Technical marketers who need reproducible diagnostics
- QA, web ops, and platform engineers troubleshooting production behavior

## Why this matters in a marketing engineering workflow

A browser often hides the true request/response path. `curl` gives you a deterministic way to verify:

- Exact status codes (`200`, `301`, `302`, `403`, `404`, `5xx`)
- Redirect chains and canonical destination consistency
- User-Agent-specific behavior (browser vs crawler vs default client)
- Header-level policy (cache, robots, cookies, content type)
- Session and cookie effects on access and routing
- API/form endpoint behavior for integrations and lead flows

## Common use cases in Stubgroup-style delivery

Use the cheatsheet when you need to quickly answer questions like:

- Is the root URL redirecting correctly from HTTP to HTTPS?
- Do `www` and non-`www` resolve consistently?
- Is a WAF/CDN blocking certain clients but allowing others?
- Does localization depend on `Accept-Language`, cookies, or UA?
- Is Googlebot seeing a different route than Chrome?
- Are canonical signals and HTML domain references aligned?
- Are API endpoints and CORS preflight configured correctly?
- Is a JavaScript-rendered SPA exposing its content to non-rendering crawlers, or returning only a loading shell?
- Are policy-required disclosures (operator attribution, Terms, Refund, Privacy) present in the raw HTML that Google Ads / AdSense review crawlers see?
- Did a build-time prerendering or SSR deploy ship correctly across every domain in a network of related sites?
- Is the response identical across user agents, or is the server varying content by UA in a way that could be classified as cloaking?

## Quick start

### 1) Prerequisites

- `curl` installed
- Terminal shell (`bash`, `zsh`, Git Bash, or PowerShell)
- A test URL or endpoint

Check version:

```bash
curl --version
```

### 2) Start with a baseline check

```bash
curl -I https://example.com/
```

Then follow redirects:

```bash
curl -I -L https://example.com/
```

### 3) Compare client identity behavior

```bash
# Default curl client
curl -I https://example.com/

# Googlebot-like UA
curl -I -A "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" https://example.com/

# Chrome-like UA
curl -I -A "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36" https://example.com/
```

### 4) Capture evidence for tickets/incidents

```bash
curl -D headers.txt -o body.html https://example.com/
```

This allows developers to attach concrete artifacts to bug reports, client updates, or incident timelines.

## How to use the full cheatsheet effectively

Recommended reading order in `curl-website-screening-cheatsheet.md`:

1. **Basic website screening**
2. **User-Agent parity tests**
3. **Redirect/canonical validation**
4. **Header inspection**
5. **Cookie/session testing**
6. **Verbose debugging and timing checks**
7. **POST/API and CORS checks**
8. **Content retrieval and pattern checks**
9. **SPA, prerendering, and policy disclosure audits**
10. **Practical workflows**

If you're new, start with Workflows A–F and then drill into command sections for deeper analysis. Workflow F specifically covers the SPA / policy-review use case (Google Ads disclosure visibility, build-time prerendering verification, cloaking-shape detection).

## Operational best practices

- **Always test multiple identities**: default `curl`, Googlebot-like UA, AdsBot-like UA, and browser-like UA.
- **Test both transport and hostname variants**: `http`, `https`, `www`, non-`www`.
- **Preserve artifacts**: save headers/body for reproducibility.
- **Avoid one-command conclusions**: compare at least 2–3 controlled requests.
- **Run checks from the same network environment** when diagnosing geofenced/CDN behavior.
- **Document timestamp and command used** in tickets and client communications.
- **For SPAs and JS-rendered sites, fetch the raw body and verify content presence directly** — don't rely on browser-rendered output. A page that looks correct in Chrome may return a near-empty shell to any client that doesn't execute JavaScript, including paid-media policy review crawlers.
- **Use `md5sum` to confirm byte-identical responses across user agents** when verifying a no-cloaking fix. One hash per UA is cleaner evidence than a multi-line `diff` and is easy to attach to an appeal or audit trail.

## Suggested team workflow

1. Reproduce issue with baseline `curl -I`.
2. Expand with `-L` and UA variations.
3. Capture headers/body artifacts.
4. Validate cookie/session dependencies.
5. Confirm canonical + redirect + HTML consistency.
6. For JS-rendered sites: inspect the raw body (`curl -s | wc -c` and content grep) before concluding content is present.
7. Post findings with exact commands and outputs in issue tracker.

## Windows note

In PowerShell, use `curl.exe` to avoid alias confusion:

```powershell
curl.exe -I https://example.com/
```

## Learning references

The full cheatsheet already includes high-quality references, including:

- Everything curl
- Official curl man page
- HTTP scripting with curl
- MDN HTTP method/header/redirect docs

Refer to the bottom section of `curl-website-screening-cheatsheet.md` for direct links.

## Contributing

When updating this repository:

- Keep commands copy/paste friendly.
- Prefer realistic diagnostic workflows over isolated examples.
- Add short “what it does” and “use it for” notes for every command.
- Favor developer readability and incident reproducibility.

---

For the complete command catalog and detailed workflows, read:

- `curl-website-screening-cheatsheet.md`
