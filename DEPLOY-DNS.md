# DNS & Deploy Setup — `komida.com.my`

How the live site is wired up: GitHub Pages serves the static files, Cloudflare handles DNS.

---

## Current state

- [x] GitHub repo `ahmadfayyadh17/komida-landing`
- [x] GitHub Pages enabled (Source: `main` / root)
- [x] `CNAME` file in repo contains `komida.com.my`
- [x] Domain `komida.com.my` on Cloudflare (zone status: active)
- [x] Cloudflare nameservers: `cullen.ns.cloudflare.com`, `pola.ns.cloudflare.com`
- [x] GitHub Pages custom domain set to `komida.com.my` (DNS check successful)
- [x] HTTPS enforced (Let's Encrypt cert auto-issued)
- [x] Live: https://komida.com.my/

---

## DNS records on Cloudflare

DNS-only mode (grey cloud / `proxied: false`) — no CDN proxy in front of GitHub Pages.

| Type  | Name              | Content                          | Proxied |
|-------|-------------------|----------------------------------|---------|
| A     | `komida.com.my`   | `185.199.108.153`                | DNS only |
| A     | `komida.com.my`   | `185.199.109.153`                | DNS only |
| A     | `komida.com.my`   | `185.199.110.153`                | DNS only |
| A     | `komida.com.my`   | `185.199.111.153`                | DNS only |
| CNAME | `www`             | `ahmadfayyadh17.github.io`       | DNS only |

The four A records are the published GitHub Pages IPs (load-balanced).
The CNAME redirects `www.komida.com.my` → the GitHub Pages canonical name.

Email routing (MX + TXT records) is also on Cloudflare — leave those untouched.

---

## How a deploy works

1. Push to `main` on the GitHub repo.
2. GitHub Pages auto-builds within ~1 min.
3. Cloudflare DNS already points at GitHub Pages, so the new version is live at `https://komida.com.my/` immediately after the build.

No DNS, Cloudflare, or build pipeline changes needed for content updates — just push.

---

## Verifying a deploy

- Visit https://komida.com.my/ — should load latest commit.
- GitHub repo → **Settings → Pages** should show "Your site is live at https://komida.com.my/" with "Last deployed N minutes ago".
- Hard-refresh (Ctrl+Shift+R) if you don't see the change — browser cache, not deploy lag.

---

## Optional: turn on Cloudflare CDN/DDoS proxy

Currently set to DNS-only. If you want Cloudflare's CDN cache, bot protection, and DDoS shield in front:

1. In Cloudflare DNS, flip each A record and the `www` CNAME from grey cloud → orange cloud (`proxied: true`).
2. In Cloudflare **SSL/TLS → Overview**, set encryption mode to **Full** (NOT "Full (strict)" — GitHub Pages cert won't satisfy strict).
3. Wait ~5 min, test https://komida.com.my/.

Trade-offs to know first:
- Cache rules need tuning, or content updates may take longer to appear (use Cloudflare "Purge Everything" after a deploy until rules are right).
- GitHub Pages still does its own HTTPS — you end up double-encrypted, which is fine but wastes a bit of CPU.
- Page Rules / Caching settings live on Cloudflare, not in this repo.

Not urgent — current direct-to-GitHub setup is working fine.

---

## Troubleshooting

**Site returns 404 at `komida.com.my`**
→ Check `CNAME` file in repo root contains exactly `komida.com.my` (no trailing newline issues, no `www.`).
→ Check GitHub repo → Settings → Pages → Custom domain shows `komida.com.my` with green "DNS check successful".

**`www.komida.com.my` doesn't redirect to apex**
→ Verify the `www` CNAME on Cloudflare points to `ahmadfayyadh17.github.io` (not the custom domain).

**HTTPS warning / mixed content**
→ Check "Enforce HTTPS" is ticked under GitHub Pages settings.
→ Cert auto-renews via Let's Encrypt; can take up to 24 hr after a DNS change.

**Latest push not showing**
→ Check Actions tab on the repo — `pages build and deployment` workflow should be green.
→ Hard-refresh browser; check in incognito.

---

## Reference

- GitHub Pages custom domain docs: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site
- GitHub Pages IPs: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain
- Cloudflare zone: https://dash.cloudflare.com/
- MYNIC (`.my` registrant rules): https://www.mynic.my/
