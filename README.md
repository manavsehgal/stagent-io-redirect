# stagent-io-redirect

Path-preserving redirect from `stagent.io` to `ainative.business`.

## Why per-path HTML files

The original site at this domain was rebranded and moved to `ainative.business`.
For Google Search Console's site-move tool to verify the migration, each
sample URL on the old domain has to:

1. Return HTTP 200 (not 404).
2. Carry a path-specific `<link rel="canonical">` pointing to the matching
   URL on the new domain (so each old URL has a unique destination, not
   "all paths redirect to the apex").

GitHub Pages cannot emit HTTP 301 redirects, but it can serve any number of
static `index.html` files at any number of paths. So this repo holds one
small redirect HTML per known stagent.io URL — generated from the
`ainative.business` sitemap by
`scripts/generate-stagent-redirects.mjs` in the `ainative-business.github.io`
repo and committed here.

Each redirect HTML uses three layered mechanisms so any client lands on the
correct destination:

- `<meta http-equiv="refresh">` for clients that don't run JavaScript.
- `<link rel="canonical">` for search engines (path-specific, per-URL).
- `<script>location.replace(...)</script>` for browsers (instant, preserves
  query and hash).

## Limitations

- This is still **meta-refresh**, not HTTP 301. Search Console site-move
  will likely still warn "Recommended: 301-redirects from sample pages."
  Google still processes the move, just with weaker authority signal than a
  real 301.
- For a proper HTTP 301 setup, move `stagent.io` DNS to a host that supports
  redirect headers (Cloudflare Bulk Redirects, Vercel, Netlify) and serve
  `https://stagent.io/*` → `https://ainative.business/$1` as 301.

## Files

- `index.html` — apex (`stagent.io/`).
- `<path>/index.html` — one per stagent.io-era URL, each canonicalizing to
  its matching ainative.business destination.
- `404.html` — fallback for any path the generator didn't pre-create. Still
  HTTP 404, but the JavaScript performs path-preserving navigation for
  human visitors.
- `CNAME` — associates this Pages site with `stagent.io`.

## Regenerating

In the `ainative-business.github.io` repo:

```bash
npm run build  # ensures dist/sitemap-0.xml is current
node scripts/generate-stagent-redirects.mjs /path/to/stagent-io-redirect
```

The script reads the website's sitemap, filters out post-rebrand additions
(`/field-notes/*`, `/fieldkit/*`), and writes one redirect HTML per
remaining path. The 3 historical `/research/*` paths are mapped to their
`/field-notes/*` destinations explicitly.
