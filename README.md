# stagent-io-redirect

Path-preserving redirect from `stagent.io` to `ainative.business`.

The original site at this domain was rebranded and moved. This minimal GitHub
Pages repo serves a single `index.html` that:

- Sets `<meta http-equiv="refresh">` for non-JS clients.
- Sets `<link rel="canonical">` for search engines.
- Runs `location.replace(...)` so deep links preserve path, query, and hash.

DNS for `stagent.io` points to GitHub Pages IPs. The `CNAME` file in this repo
associates the domain with this Pages site.
