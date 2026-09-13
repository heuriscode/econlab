# EconLab landing page — repo setup and deployment brief

Hand this to a coding chat. Everything needed is in `index.html` (single file, no build step).

## What this is

A single-page marketing site for **EconLab**, a quantitative economics consultancy operating as a
trading name of **Heuris Pty Ltd**. Static HTML, inline CSS, ~30 lines of vanilla JS. No framework,
no bundler, no dependencies beyond Google Fonts loaded via `@import`.

Target: **GitHub Pages**, custom domain **econlab.com.au** (owned).

## Files

| File | Purpose |
|---|---|
| `index.html` | The entire site. Deploy as-is at repo root. |
| `CNAME` | Custom domain declaration for GitHub Pages. Must sit at repo root. Contents: `econlab.com.au` |
| `project-template.json` | Copy-paste template for adding projects. Reference only — not served. |
| `photos/` | Profile photos (see below). Create this folder at repo root. |

## Repo setup

1. Create a public repo (name is arbitrary; `econlab-site` is fine).
2. Commit `index.html` and `CNAME` to the root of `main`.
3. Settings → Pages → Source: **Deploy from a branch** → Branch `main`, folder `/ (root)`.
4. Settings → Pages → Custom domain: enter `econlab.com.au`, save.
5. Wait for the certificate to issue, then tick **Enforce HTTPS**.

## DNS (at the registrar holding econlab.com.au)

Apex domain — four A records pointing at GitHub Pages:

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

Optionally add AAAA records for IPv6:

```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

Plus a CNAME record for `www` → `<github-username>.github.io`

DNS propagation typically takes 15 minutes to a few hours. GitHub's certificate issues after DNS
resolves, and can take up to 24 hours.

## Profile photos

Each person's avatar is a slot that **falls back to their initials if no image file exists**, so the
site works right now with none of these present. Drop files in a `photos/` folder at repo root:

```
photos/daniel-gregg.jpg
photos/megan-star.jpg
photos/john-kandulu.jpg
photos/iain-fraser.jpg
photos/jeff-connor.jpg
```

Square crops, 400×400px or larger, head and shoulders. They're rendered circular with
`object-position: center top`, so leave a little headroom. Filenames must match exactly —
lowercase, hyphenated, `.jpg`. To use `.png`, update the `src` in the matching card.

Adding a photo for a new associate: the template card in the `#team` section already includes the
`<img>` tag; change the `src` filename and the `alt` text.

## Contact form — the actual question

The site currently uses **`mailto:` links only** (`contact@econlab.com.au`), in the hero CTA
button and the contact section. This works today with zero setup.

GitHub Pages serves static files only — **there is no server-side code, so a real `<form>` cannot
post anywhere without a third-party endpoint.** Three options, in order of effort:

**Option A — keep `mailto:` (current state, zero setup).**
Works everywhere, but opens the visitor's mail client, which some corporate users have unconfigured.
Fine for launch.

**Option B — Formspree or similar (recommended once traffic matters).**
Sign up at formspree.io, create a form, get an endpoint ID, then add a real form to the contact
section:

```html
<form action="https://formspree.io/f/YOUR_FORM_ID" method="POST">
  <input type="email" name="email" placeholder="Your email" required>
  <input type="text" name="organisation" placeholder="Organisation">
  <textarea name="message" placeholder="What are you working on?" required></textarea>
  <button type="submit" class="btn btn-gold btn-xl">Send</button>
</form>
```

Submissions are forwarded to whichever address you nominate. Free tier covers 50 submissions/month.
Alternatives with the same pattern: Web3Forms, Basin, Netlify Forms (requires Netlify hosting).

**Option C — make `contact@econlab.com.au` actually receive mail. DO THIS FIRST.**
The page already publishes `contact@econlab.com.au`, but **that mailbox does not exist yet** — mail
sent to it will bounce until it's set up. Options: a free forwarding service (ImprovMX aliases
`contact@econlab.com.au` → your Heuris address, about ten minutes, requires adding two MX records),
or a full mailbox via Google Workspace or Fastmail. **Do not publish the site until this resolves**,
or enquiries will silently fail.

## The hero positioning line

Under the headline sits one italic line, marked in `index.html` with an HTML comment
(`POSITIONING LINE`). It currently reads:

> The depth of an in-house quant team, on the projects that need it.

Alternates, if you want to test a different angle — swap the text, nothing else changes:

- *Research-grade quantitative capability, without carrying it year-round.*
- *The quantitative team your next bid needs, for exactly as long as it needs one.*
- *Specialist econometrics, on call.*

## Placeholders to resolve before launch

Search `index.html` for these:

- `contact@econlab.com.au` — must be a live mailbox before launch (see Option C above).
- `(+61) 476 184 905` — 1 occurrence, on Daniel Gregg's director card. Remove if you'd rather not
  publish a mobile number.
- ABN — **not currently on the page.** Australian business practice expects it in the footer;
  add to the `.footer-copy` div.
- The associate card template — an HTML comment block in the `#team` section marked
  `ASSOCIATE TEMPLATE`. Copy that block per new associate. The grid is
  `repeat(auto-fill, minmax(255px, 1fr))` so it reflows for any number of cards without CSS changes.

## Maintenance notes for whoever edits next

- **Adding an associate:** duplicate the commented template block in `#team` → `.assoc-grid`.
  The grid is `repeat(auto-fill, minmax(330px, 1fr))`, so any number of cards reflows on its own.
- **Adding a project:** edit the `PROJECTS` array near the bottom of `index.html`. See
  `project-template.json` for a copy-paste block and field guide. The selector pills, the slide,
  the prev/next arrows and the counter are all generated from that array — no markup to touch.
- **Colour system** is CSS custom properties in `:root` (the `--eg-*` ramp). Change the palette there,
  not in individual rules.
- **Contrast is the whole hierarchy.** The palette is monochrome, so legibility rests entirely on
  the lightness steps in the `--eg-*` ramp. Every text/background pair currently passes WCAG AA.
  If anyone lightens the mid-greys, run a contrast check before shipping — there's no hue left to
  carry meaning if the ratios go.

## Suggested additions (not built)

- `robots.txt` and a minimal `sitemap.xml` for indexing.
- Open Graph / Twitter card meta tags plus a share image — matters when the URL gets pasted into
  Slack or LinkedIn during business development.
- A favicon: the nav SVG logo mark can be extracted and saved as `favicon.svg`.
