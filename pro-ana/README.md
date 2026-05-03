# Pi-hole Pro-Ana / Thinspo Blocklist

A DNS-level blocklist for pro-anorexia, pro-bulimia, and "thinspiration"
content. Blocks dedicated forums and uses regex to catch new variants.

## Files

| File | Purpose |
|------|---------|
| `blocklist.txt` | Domains of dedicated pro-ana forums and historical sites. |
| `regex.txt`     | Regex rules to catch unlisted domains containing pro-ana vocabulary. |
| `allowlist.txt` | Recovery/treatment domains, pre-allowed against false positives. |

## Install (Pi-hole)

1. **Domain blocklist** — host the file somewhere reachable by the Pi-hole, then:
   - Web UI → **Adlists** → paste the URL → **Add**
   - `pihole -g` (update gravity)
   - Or paste contents into `/etc/pihole/custom.list` and run `pihole -g`.

2. **Regex rules** — Web UI → **Group Management → Domains → RegEx filter**.
   Paste each pattern as its own row. Or via CLI:
   ```sh
   while read -r p; do
     [[ "$p" =~ ^#|^$ ]] && continue
     pihole --regex "$p"
   done < regex.txt
   ```

3. **Allowlist** — Web UI → **Group Management → Domains → Whitelist**.
   Or:
   ```sh
   while read -r d; do
     [[ "$d" =~ ^#|^$ ]] && continue
     pihole -w "$d"
   done < allowlist.txt
   ```

## Verification

```sh
pihole -q myproana.com                    # expect: matched
pihole -q nationaleatingdisorders.org     # expect: not blocked
dig @<pihole-ip> myproana.com             # expect: 0.0.0.0 / NXDOMAIN
```

## What this DOES block

- Standalone pro-ana forums: MyProAna, Fading Obsessions, MiAna Place,
  Skinny Gossip, plus a handful of historical domains from 2010-era
  academic content analyses.
- Any FQDN containing characteristic vocabulary: `proana`, `promia`,
  `thinspo`, `bonespo`, `deathspo`, `meanspo`, `thighgap`, etc.

## What this does NOT block

DNS works at the whole-domain level. The list intentionally avoids these
because blocking the parent domain would break legitimate use:

- Major social platforms (Reddit, X/Twitter, Tumblr, Instagram, TikTok,
  Pinterest, YouTube) — pro-ana content thrives here but blocking them
  is a separate decision with much wider impact.
- Discord (`discord.com`, `discord.gg`) and Telegram (`t.me`) — same
  reasoning.
- Generic blog hosts (Wix, Blogspot, WordPress.com subdomains) — parent
  is shared infrastructure.

If you need to block a specific subreddit, Tumblr blog, or Discord
server, that requires URL-level filtering — a browser extension
(uBlock Origin custom rules, LeechBlock NG) or a forward proxy. Not
something Pi-hole can do.

## Caveats

- **Regex false positives.** `pro-ana` can collide with `pro-anal*`
  domains (e.g. `pro-analytics.com`). If you hit one, add it to the
  allowlist. Validate any suspect domain with `pihole -q <domain>`.
- **Maintenance.** Pro-ana sites move and rebrand often (MyProAna →
  Eating Disorder Support Forum is the canonical example). Re-verify
  this list every ~6 months.
- **Not a substitute for support.** A blocklist reduces exposure but
  does not treat an eating disorder.

## If you or someone you know is struggling

- **NEDA (US):** call 1-800-931-2237, text "NEDA" to 741741, or visit
  https://www.nationaleatingdisorders.org
- **Beat (UK):** https://www.beateatingdisorders.org.uk
- **Butterfly Foundation (AU):** https://butterfly.org.au

## Sources

- Wikipedia, *Pro-ana*: https://en.wikipedia.org/wiki/Pro-ana
- Borzekowski et al., *e-Ana and e-Mia: A Content Analysis of
  Pro–Eating Disorder Web Sites*, AJPH 2010 / PMC2901299:
  https://pmc.ncbi.nlm.nih.gov/articles/PMC2901299/
- Rouleau & von Ranson, *Psychological Impact of Pro-Anorexia
  and Pro-ED Websites on Adolescent Females: A Systematic Review*,
  PMC 2021: https://pmc.ncbi.nlm.nih.gov/articles/PMC7926357/
- Lutz, *The Thin Commandments: A Content Analysis of "Pro-Ana"
  Websites* (Saint Mary's College).
- arXiv 2412.15721, *Safe Spaces or Toxic Places? Content
  Moderation and Social Dynamics of Online Eating Disorder
  Communities* (Dec 2024).
- The FADER, *Indie sleaze nostalgia has revived Y2K's "thinspo"
  community* (2024-10-15).
- Tech Transparency Project, *Thinstagram*.
- Chancellor et al., *#thyghgapp: Instagram Content Moderation
  and Lexical Variation in Pro-Eating Disorder Communities*
  (Georgia Tech).
