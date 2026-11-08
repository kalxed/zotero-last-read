# Zotero Reading (Tronbyt app)

Shows the paper you most recently **highlighted** in Zotero on a Tronbyt/Tidbyt: title, authors, your name and the Zotero logo. Highlights are the signal, so papers you open but never annotate don't count.

![preview](zoteroreading/zoteroreading@2x.webp)

## Layouts

- **Simple**: the latest paper.
- **With stats**: alternates with your streak (consecutive days with a highlight) and highlights this week.
- **Reading buddies**: two people, one per half of the screen.

After N days without highlights (default 7), the paper is replaced by a rotating motivational message. Tick **Show days since last read** to keep the paper on screen with "N days ago" instead.

## Setup

1. Go to zotero.org → Settings → Security → Applications → **Create new private key** (or https://www.zotero.org/settings/keys/new).
2. Tick **Allow library access** and leave write access off. Save, then copy the key.
3. The same page shows "Your user ID for use in API calls is …". That number is your user ID.
4. Add the app on your Tronbyt and fill in name, user ID and key. For Reading buddies, fill in the buddy fields too.

Only your personal library is read (not group libraries). Zotero is asked at most once every 15 minutes.

## How it works

- Zotero Web API v3 (`api.zotero.org`, `Zotero-API-Key` header). Highlights are `itemType=annotation` items, sorted by `dateAdded` desc, 100 per page.
- Newest annotation → `parentItem` (the PDF attachment) → its `parentItem` (the paper) → title and creators. Deleted parents fall back to the attachment title or "Untitled".
- Annotation dates are bucketed into days in the device timezone (`time.tz()`). The streak stays alive through the day after the last highlight. Paging stops once the streak ends inside a page (max 5 pages).
- Every request is cached for 15 minutes (`http.get(ttl_seconds=900)`).
- Errors: 403 → "Check API key", 404 → "Check user ID", other → "Zotero offline", half-filled config → "Finish setup". With no config at all, it renders demo data.

## Development

Needs Tronbyt's pixlet (v0.55+). Tidbyt's v0.34 rejects secret fields.

```sh
gh release download v0.55.0 -R tronbyt/pixlet -p 'pixlet_v0.55.0_darwin-arm64.tar.gz' -O - | tar -xz -C bin
bin/pixlet render zoteroreading/zoteroreading.star selftest=true   # logic self-check, exits non-zero on failure
bin/pixlet serve zoteroreading/zoteroreading.star                  # demo data when no key is set
bin/pixlet check zoteroreading/zoteroreading.star
```

---

Built with [Claude Code](https://claude.com/claude-code).
