# Pi-hole Self-Harm / Pro-Self-Injury Blocklist

A DNS-level blocklist for sites that promote, glorify, or instruct on
self-harm, non-suicidal self-injury (NSSI), or suicide.

## Files

| File | Purpose |
|------|---------|
| `blocklist.txt` | Domains of documented suicide-encouragement and pro-self-harm sites. |
| `regex.txt`     | Regex rules to catch unlisted domains containing characteristic vocabulary. |
| `allowlist.txt` | Recovery / crisis-support domains, pre-allowed against false positives. |

## Install (Pi-hole)

1. **Domain blocklist** — Web UI → **Adlists** → paste this file's URL → **Add**,
   then `pihole -g`.

2. **Regex rules** — Web UI → **Group Management → Domains → RegEx filter**.
   Or:
   ```sh
   while read -r p; do
     [[ "$p" =~ ^#|^$ ]] && continue
     pihole --regex "$p"
   done < regex.txt
   ```

3. **Allowlist** — important; recovery sites use the same vocabulary.
   ```sh
   while read -r d; do
     [[ "$d" =~ ^#|^$ ]] && continue
     pihole -w "$d"
   done < allowlist.txt
   ```

## Verification

```sh
pihole -q sanctioned-suicide.net   # expect: matched
pihole -q recoveryourlife.com      # expect: not blocked (recovery site)
pihole -q 988lifeline.org          # expect: not blocked (crisis support)
```

## What this DOES block

- **`sanctioned-suicide.net` / `.org` / `.com`** — the suicide-method
  forum that UK ISPs (Sky, BT, Virgin Media, TalkTalk) added to default
  safety blocking in 2024 following BBC investigation. Linked in
  reporting and litigation to multiple deaths, including 17-year-old
  Vlad Nikolin-Caisley (2024). Subject to ongoing Ofcom investigation
  under the Online Safety Act.
- **`icut.livejournal.com`** — long-running NSSI LiveJournal community
  (subdomain only; LiveJournal itself is not blocked).
- **Any FQDN containing** `selfharm`, `self-harm`, `selfinjury`,
  `self-injury`, `selfmutilation`, `self-mutilation`, `prosh`,
  `pro-self-harm`, `secretsociety123`, `mysecretfamily`,
  `sanctionedsuicide`, or the algospeak `unalive`.

## What this does NOT block

- **Major social platforms** (TikTok, Instagram, Twitter/X, Tumblr,
  Reddit, Discord, Telegram). NSSI content thrives here but blocking
  the parent domain is too broad.
- **Bare codewords** from the *Secret Society 123* research (`cat`,
  `blithe`, `beans`, `Addie`, `Annie`, `Olive`, `Sue`, etc.). These
  are real names and common words — false-positive risk is unacceptable
  at the DNS layer.
- **Algospeak character substitutions** like `$h`, `s/h`, `3lf-h4rm`.
  The literal `$` and `/` characters cannot appear in DNS labels, so
  they cannot show up in domain names anyway. Bare `sh` would collide
  with shell, share, ship, shop, etc.
- **Generic words** like `cutting`, `cuts`, `cutter`, `blade`. They
  collide with laser cutting, hairdressing, knife retailers, etc.

If you need to block specific subreddits, TikTok hashtags, Tumblr
blogs, or Discord servers, that requires URL- or content-level
filtering (browser extension, parental control proxy) — Pi-hole
operates at the DNS layer only.

## Caveats

- **Recovery sites use the same vocabulary.** The allowlist covers
  the major ones (`recoveryourlife.com`, `selfinjury.bctr.cornell.edu`,
  `7cups.com`, `thesira.org`, `healthyplace.com`, `nami.org`,
  `988lifeline.org`, etc.) but if you find a recovery site getting
  caught, add it to `allowlist.txt`.
- **`unalive` is rapidly evolving algospeak.** It is increasingly used
  in legitimate creator content discussing suicide *prevention*. If
  this causes issues, comment it out of `regex.txt`.
- **Maintenance.** Re-verify the dedicated domains every ~6 months —
  forums move and rebrand under regulatory pressure.
- **Not a substitute for support.** A blocklist reduces exposure but
  does not replace mental health care.

## If you or someone you know is struggling

- **988 Suicide & Crisis Lifeline (US):** call or text **988**, or
  visit https://988lifeline.org
- **Crisis Text Line (US/UK/Ireland/Canada):** text **HOME** to
  **741741**, or visit https://www.crisistextline.org
- **Samaritans (UK & Ireland):** call **116 123**, or visit
  https://www.samaritans.org
- **The Trevor Project (LGBTQ+ youth, US):** 1-866-488-7386 or
  https://www.thetrevorproject.org
- **International directory:** https://www.iasp.info/resources/Crisis_Centres/

## Sources

- Wikipedia, *Sanctioned Suicide*:
  https://en.wikipedia.org/wiki/Sanctioned_Suicide
- Moreno et al., *"Secret Society 123": Understanding the Language
  of Self-Harm on Instagram*, Journal of Adolescent Health 2016 /
  PMC5322804: https://pmc.ncbi.nlm.nih.gov/articles/PMC5322804/
- Lookingbill & Le, *"There's Always a Way to Get Around the
  Guidelines": NSSI and Content Moderation on TikTok*, Social
  Media + Society 2024:
  https://journals.sagepub.com/doi/10.1177/20563051241254371
- Frontiers in Digital Health, *Digital mental health and hidden
  support: NSSI communities on TikTok* (2025):
  https://www.frontiersin.org/articles/10.3389/fdgth.2025.1645276/
- NYU JLPP, *Closing the Online Suicide Assistance Loophole* (2024):
  https://nyujlpp.org/wp-content/uploads/2024/03/JLPP-26.1-Fisher.pdf
- Tumblr Staff, *Follow-up: Tumblr's New Policy Against
  Pro-Self-Harm Blogs* (2012).
- Cash et al., *Adolescent Suicide Statements on MySpace*, and
  Lewis et al. on Tumblr NSSI content.
