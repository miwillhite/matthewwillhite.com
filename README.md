# matthewwillhite.com

Static landing page. No build step. Edit `index.html`, push to `main`, GitHub Pages redeploys.

## One-time setup (about 20 minutes, then DNS wait)

### 1. Create the repo and push

```bash
cd matthewwillhite.com        # this folder
git init -b main
git add .
git commit -m "Landing page v1"
gh repo create miwillhite/matthewwillhite.com --public --source=. --push
# or create the repo in the GitHub UI and: git remote add origin git@github.com:miwillhite/matthewwillhite.com.git && git push -u origin main
```

Keep the repo public. The repo itself is part of the GitHub signal.

### 2. Turn on Pages

GitHub repo → Settings → Pages:

- Source: Deploy from a branch
- Branch: `main`, folder `/ (root)`
- Custom domain: `matthewwillhite.com` (the `CNAME` file in the repo already says this; entering it here too keeps them in sync)
- Leave "Enforce HTTPS" for after DNS resolves (step 4).

### 3. Namecheap DNS

Namecheap → Domain List → matthewwillhite.com → Manage → Advanced DNS. Delete the parking records Namecheap added (the `CNAME www → parkingpage.namecheap.com` and the `URL Redirect` record). Then add:

| Type  | Host | Value                  | TTL       |
|-------|------|------------------------|-----------|
| A     | @    | 185.199.108.153        | Automatic |
| A     | @    | 185.199.109.153        | Automatic |
| A     | @    | 185.199.110.153        | Automatic |
| A     | @    | 185.199.111.153        | Automatic |
| CNAME | www  | miwillhite.github.io.  | Automatic |

Those four A records are GitHub's current Pages IPs. If GitHub ever changes them, the list lives at https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site

Check from a terminal (propagation is usually minutes, sometimes an hour):

```bash
dig +short matthewwillhite.com A
dig +short www.matthewwillhite.com CNAME
```

### 4. HTTPS

Back in repo Settings → Pages. Once the custom-domain check shows a green tick, enable "Enforce HTTPS". GitHub issues the Let's Encrypt cert itself; nothing to manage. `www.matthewwillhite.com` will redirect to the apex.

### 5. Tell Google the site exists (do this the same day)

1. https://search.google.com/search-console → Add property → **Domain** type → `matthewwillhite.com`.
2. Google gives you a TXT record. Add it in Namecheap Advanced DNS: Type `TXT`, Host `@`, Value `google-site-verification=...`. Verify.
3. In Search Console → Sitemaps → submit `https://matthewwillhite.com/sitemap.xml`.
4. URL Inspection → paste `https://matthewwillhite.com/` → Request indexing.

Also worth doing the same day, five minutes total:

- Bing Webmaster Tools (https://www.bing.com/webmasters) → import from Search Console. Bing feeds ChatGPT search and Copilot.
- LinkedIn profile → Contact info → add `https://matthewwillhite.com`. Also put it in the Featured section.
- GitHub profile → Edit profile → Website `https://matthewwillhite.com`. This closes the `sameAs` loop: the site points at LinkedIn and GitHub, and both point back.

## Baseline measurement (take today, before anything indexes)

Screenshot or paste into a note:

- Google and Bing results for `"Matthew Willhite" engineer` and `Matthew Willhite FloQast`
- What ChatGPT, Claude, and Perplexity answer to "Who is Matthew Willhite, the software engineer in Camas, Washington?"
- LinkedIn → Analytics → search appearances (weekly number)

Repeat quarterly. The first month will look like nothing happened. That is normal.

## Updating

- Content: edit `index.html`, commit, push. Live in about a minute.
- Any time content changes, bump `lastmod` in `sitemap.xml` and `dateModified` in the JSON-LD block at the top of `index.html`.
- Adding a case study later: create `writing/<slug>/index.html`, add it to `sitemap.xml`, link it from the Writing section. Each article gets its own `Article` JSON-LD block with `author` pointing at `https://matthewwillhite.com/#person`.

## What's in here

| File          | Purpose |
|---------------|---------|
| `index.html`  | The page. Inline CSS, no JS, no external requests. JSON-LD `ProfilePage` + `Person` with `sameAs` to LinkedIn and GitHub. |
| `404.html`    | GitHub Pages serves this for missing paths. |
| `CNAME`       | Custom domain for Pages. Do not delete. |
| `.nojekyll`   | Tells Pages to serve files as-is. |
| `robots.txt`  | Allows all crawlers, points at the sitemap. |
| `sitemap.xml` | One URL today; grows with the writing. |
