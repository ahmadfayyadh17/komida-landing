# Custom Domain Setup — `www.komida.my`

Step-by-step to point `komida.my` (registered via Exabytes) at this GitHub Pages site.

---

## Status checklist

- [x] GitHub repo `komida-landing` created
- [x] GitHub Pages enabled (Source: main / root)
- [x] Site live at `https://ahmadfayyadh17.github.io/komida-landing/`
- [x] `CNAME` file committed (tells GitHub to expect `www.komida.my`)
- [ ] Domain `komida.my` purchased via Exabytes
- [ ] MYNIC verification (upload SKM cert when Exabytes asks)
- [ ] DNS records added in Exabytes panel
- [ ] Custom domain set in GitHub repo settings
- [ ] HTTPS enabled

---

## Step 1 — In the Exabytes Client Area (after purchase)

1. Log in at https://billing.exabytes.my/
2. Go to **Domains → My Domains**
3. Click `komida.my` → **Manage DNS** (or "Nameservers / DNS Manager")
4. **Add these records** (delete any existing default records first):

| Type  | Host / Name | Value / Points to              | TTL  |
|-------|-------------|--------------------------------|------|
| A     | `@`         | `185.199.108.153`              | 3600 |
| A     | `@`         | `185.199.109.153`              | 3600 |
| A     | `@`         | `185.199.110.153`              | 3600 |
| A     | `@`         | `185.199.111.153`              | 3600 |
| CNAME | `www`       | `ahmadfayyadh17.github.io.`    | 3600 |

> `@` means the apex domain (`komida.my` with no `www`).
> The four A records load-balance across GitHub's Pages servers.
> The trailing dot on the CNAME value is required by some panels — omit if Exabytes rejects it.

---

## Step 2 — In GitHub repo settings

1. Go to https://github.com/ahmadfayyadh17/komida-landing/settings/pages
2. Under **Custom domain**, type: `www.komida.my`
3. Click **Save**
4. GitHub will check DNS — show "DNS check in progress" then "DNS check successful" (5 min – 24 hr)
5. Once the check passes, the **Enforce HTTPS** checkbox unlocks — tick it. Free Let's Encrypt cert issues automatically (~10 min).

---

## Step 3 — Verify

Test from a fresh browser (no cache):
- https://www.komida.my/ → should load your landing page
- https://komida.my/ → should redirect to www.komida.my
- Padlock in address bar = HTTPS working

---

## DNS propagation check

If it's not loading after 1 hour, check propagation status at:
- https://dnschecker.org/#A/komida.my
- https://dnschecker.org/#CNAME/www.komida.my

You want all green checkmarks pointing to GitHub's IPs (`185.199.108-111.153`).

---

## Troubleshooting

**"DNS check failed" in GitHub Pages**
→ DNS hasn't propagated yet. Wait 30 min and click "Re-check".

**Site loads at `www.komida.my` but apex `komida.my` doesn't work**
→ A records on `@` missing or wrong. Re-check in Exabytes.

**Mixed content warning / no HTTPS**
→ Wait — Let's Encrypt cert issuance can take up to 24 hr after the DNS check passes.

**Want to switch primary domain to apex (`komida.my` not `www.komida.my`)**
→ Change `CNAME` file in repo to `komida.my` and update GitHub Pages custom domain field. Note: apex domains can't use CNAME so you must rely on A records.

---

## Reference

- GitHub Pages docs on custom domains: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site
- MYNIC `.my` registrant rules: https://www.mynic.my/
- GitHub Pages IP addresses (don't change often): https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain
