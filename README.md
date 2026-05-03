# Pro-anorexia, pro-self-harm, and pro-suicide blocklist 

DNS-level blocklists. Each category is self-contained:
a domain blocklist, a regex blocklist, and an allowlist of
recovery/support resources that share vocabulary with the blocked
content.

| Category | Description |
|----------|-------------|
| [`pro-ana/`](pro-ana/) | Pro-anorexia, pro-bulimia, and "thinspiration" forums and blogs. |
| [`self-harm/`](self-harm/) | Pro-self-harm, NSSI-glorification, and suicide-encouragement sites. |

## How to use

Each category folder contains three files plus a README:

- `blocklist.txt` — plain domain list, one per line. Import via
  Pi-hole **Adlists** (paste the raw GitHub URL).
- `regex.txt` — regex rules. Import via **Group Management →
  Domains → RegEx filter**.
- `allowlist.txt` — recovery/support domains. Import via
  **Group Management → Domains → Whitelist**.

See each category's README for category-specific installation commands,
verification steps, and lists of what is and isn't blocked.

### Quick install (all categories, via shell)

If you have shell access to the Pi-hole and want to import everything
in one go:

```sh
git clone https://github.com/<your-username>/<this-repo>.git
cd <this-repo>

for cat in pro-ana self-harm; do
  # Allowlist FIRST so it survives any subsequent regex collisions.
  while read -r d; do
    [[ "$d" =~ ^#|^$ ]] && continue
    pihole -w "$d"
  done < "$cat/allowlist.txt"

  while read -r d; do
    [[ "$d" =~ ^#|^$ ]] && continue
    pihole -b "$d"
  done < "$cat/blocklist.txt"

  while read -r p; do
    [[ "$p" =~ ^#|^$ ]] && continue
    pihole --regex "$p"
  done < "$cat/regex.txt"
done

pihole -g
```

### Or as hosted Adlist URLs

Once published to GitHub, the raw-file URLs work directly:

```
https://raw.githubusercontent.com/<your-username>/<this-repo>/main/pro-ana/blocklist.txt
https://raw.githubusercontent.com/<your-username>/<this-repo>/main/self-harm/blocklist.txt
```

Add each URL under **Pi-hole → Adlists**, then `pihole -g`.


## License

CC0 1.0 Universal (Public Domain Dedication). See [LICENSE](LICENSE).
Use, fork, redistribute, mirror — no attribution required, no
permission needed.

## Crisis resources

If you or someone you know is struggling:

| Region | Resource |
|--------|----------|
| UK & Ireland (crisis) | Samaritans: 116 123 — https://www.samaritans.org |
| US (suicide/crisis) | **988** call or text — https://988lifeline.org |
| US (eating disorders) | NEDA: 1-800-931-2237, text NEDA to 741741 — https://www.nationaleatingdisorders.org |
| UK (eating disorders) | Beat: 0808 801 0677 — https://www.beateatingdisorders.org.uk |
| Australia (eating disorders) | Butterfly Foundation: 1800 33 4673 — https://butterfly.org.au |
| Worldwide (crisis) | https://www.iasp.info/resources/Crisis_Centres/ |
| LGBTQ+ youth (US) | The Trevor Project: 1-866-488-7386 — https://www.thetrevorproject.org |
