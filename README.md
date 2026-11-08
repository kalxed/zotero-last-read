# Zotero Reading (Tronbyt app)

Setup and layouts: see [zoteroreading/README.md](zoteroreading/README.md).

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
