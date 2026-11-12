# Marketplace integration → Finds

The Facebook Marketplace integration on the Mac searches in a signed-in
browser. Everything it finds goes to the Finds board, which is the one
place listings and triage live. Craigslist is searched by the board itself
(daily, or Sources → Run crawl from your phone).

## Live: send what each run finds

Two calls to `POST $FINDS_URL/api/intake` with
`Authorization: Bearer $FINDS_ADMIN_TOKEN`:

1. `{ searchId, source: "Facebook Marketplace", step: "screen", candidates: [{ key, title, price, location, url }] }`
   → `{ keep: [key…] }`: the ones worth opening (all new ones when Claude
   can't screen).
2. `{ searchId, source: "Facebook Marketplace", step: "add", items: [{ key, url, title, price, location, description, photos: [url…], raw }] }`

Keys are `fb-<item id>`; the board saves them as `crawl-fb-<item id>`. Send
`add` right after opening a listing: Facebook photo links expire within
hours, and the board copies them to its own storage as they arrive. The
first photo is the card's cover.

Search ids on the board: `bed-frame`, `standing-desk`, `desk-chair`,
`sleeper-sofa`.

## Once: move an existing export

    FINDS_URL=https://finds-nate-3636s-projects.vercel.app FINDS_ADMIN_TOKEN=… \
      node scripts/send-to-finds.mjs ~/Documents/Src/marketplace/searches/*/finds-import.json

Listings that appear in more than one file go once; your status, notes and
rank stay as you set them. After that, keep working in the board.

## The admin token

Vercel → finds project → Settings → Environment Variables → `ADMIN_TOKEN`
(reveal it there). Keep it in the Mac's environment or keychain, not in
files in this repo.
