# Beast Feast Canteen 饗獸食堂 — Website

> **所屬項目：Beast Feast 饗獸食堂** ｜ 網址：https://www.beastfeast.uk （經 GitHub Pages 發佈，網域設定見下方 Hosting）

Single-page site for Beast Feast Canteen, Chapel Street, Bristol
(`https://www.beastfeast.uk/`). Static HTML, no build step.

No menu on the site — customers see the menu on the delivery apps.

## Hosting

GitHub Pages with the custom domain `www.beastfeast.uk` (see `CNAME`);
`beastfeast.uk` redirects to `www`. The domain is registered at
Squarespace; DNS records there:

| Type  | Host | Value |
|-------|------|-------|
| A     | @    | 185.199.108.153 |
| A     | @    | 185.199.109.153 |
| A     | @    | 185.199.110.153 |
| A     | @    | 185.199.111.153 |
| CNAME | www  | imager852.github.io |

## Files

- `index.html` — the page (styles and script inline).
- `assets/` — logo, hero photo, share preview (`og-image.jpg`, 1200×630
  crop of the hero photo), mascot, HungryPanda logo and QR codes
  (collection, each
  delivery app, and this site).

## Delivery platform logos

The delivery cards show each platform's logo instead of a QR code:

- `ubereats-logo.svg` — Uber Eats wordmark, taken from ubereats.com.
- `deliveroo-logo.svg`, `justeat-logo.svg` — brand marks from
  [Simple Icons](https://simpleicons.org/) v13.21.0, in brand colours.
- `hungrypanda-logo.png` — from hungrypanda.co.

`qr-ubereats.svg`, `qr-deliveroo.svg` and `qr-justeat.svg` are no longer
used on the page but are kept for flyers and printed menus.

## QR codes

Generated with [segno](https://pypi.org/project/segno/), error level M,
dark `#14100d`, 1-module border, `viewBox` so they scale:

```python
segno.make(url, error="m", micro=False).save(
    "assets/qr-NAME.svg", border=1, dark="#14100d", light="#ffffff",
    xmldecl=False, nl=False)
```

Then swap `width`/`height` for a `viewBox` + `shape-rendering="crispEdges"`.

## To do

- HungryPanda: only has a WeChat mini-program link, so the button goes
  to hungrypanda.co, not the store page.

## Stock count 盤點 (`/stockcount/`)

Mobile page for staff stock counts: https://www.beastfeast.uk/stockcount/
(`noindex`, not linked from the main page).

- **Temporary mode** (no `config.js` yet): shared code `1234` (SHA-256 checked
  in the page — not real security), item list from the public `stockcount/items.js`,
  "上次" history kept on each phone. Adding `config.js` switches to GitHub mode
  automatically; then delete `items.js`.
- **Login**: staff enter an access code. `stockcount/config.js` holds a GitHub
  token encrypted with that code (PBKDF2 600k + AES-GCM); a wrong code cannot
  decrypt it. Generate `config.js` with `stockcount/setup.html`.
- **Data lives in a private repo** (default `imager852/beastfeast-stock`), never
  in this public repo:
  - `items.json` — item list (edit to add/remove items or change expected stock)
  - `records/<date>_<time>_<staff>_<id>.json` — each count
  - `reports/….pdf` — each PDF
  - `history/latest.json` — last two counts, used for the "上次" column
- Staff count each item (−/+, type, or tap "上次" to copy last count); progress
  autosaves on the phone. "完成" → review → A4 PDF (html2pdf.js 0.10.1, vendored),
  saved to the private repo and shareable via WhatsApp/Email.
- Token: fine-grained PAT, only the private repo, Contents read/write. Anyone
  with the access code could extract it, so keep the code private and rotate
  the token (re-run setup) when staff leave.
