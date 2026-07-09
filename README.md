# মশিউরের খেরোখাতা — Mashiur's Notebook

**Live site:** https://drmashiur.github.io/ · also served at https://bn.drmashiur.com/

A personal archive of 20+ years of writing in Bengali — 500+ articles, essays,
poems, travelogues, and newspaper columns by Dr. Mashiur Rahman — published as a
fast, searchable static website.

> This repository contains only the **built website** (generated HTML, images,
> and search index). It is republished automatically from a private source
> repository — do not edit files here; changes will be overwritten.

## How this site was created

The whole archive lives in **Notion**, organized as a tree of pages (sections →
articles). A custom pipeline turns that into this website in three stages:

```text
Notion workspace  (the writing happens here)
      │
      ▼   1. SYNC — a zero-dependency Node.js engine talks to the Notion API,
      │      converts every page to Markdown, downloads all images, and stores
      │      everything in a local JSON + SQLite archive. After the first full
      │      sync, only pages edited since the last run are re-fetched
      │      (via Notion's last_edited_time), so a routine sync costs a
      │      handful of API calls.
      ▼
Local archive  (single source of truth — the site never reads Notion directly)
      │
      ▼   2. GENERATE — the archive is rendered into content files for
      │      Eleventy Notes. Sections become folders with their own pages,
      │      articles become notes with front matter (title, date, order),
      │      images are co-located, internal links become wikilinks, and each
      │      section page gets a menu of its children, mirroring Notion.
      ▼
Eleventy Notes template  (Eleventy 3 static-site generator)
      │
      ▼   3. DEPLOY — the built output is deployed to Cloudflare Workers
             (bn.drmashiur.com) and mirrored here on GitHub Pages.
```

### Interesting problems solved along the way

- **Bengali URLs.** URL slugifiers strip Bengali script entirely, which would
  leave every article with an empty, colliding URL. The pipeline includes a
  rule-based Bengali → Latin transliterator, so `প্রকাশিত বই` becomes
  `/n/articles/prokashit-boi/` while titles, navigation, and search stay fully
  Bengali. Slugs are frozen after approval, so URLs never change.
- **Bengali dates.** Publication dates like `তারিখ: ২রা এপ্রিল ২০২৩` are detected
  in article bodies (Bengali numerals, month names, ordinal suffixes, and common
  spelling variants), converted to ISO, and shown in each page's info panel.
  When only a month or year is known, it is kept as-is — a precise date is never
  invented.
- **Unicode traps.** The Bengali letters ড়/ঢ়/য় are Unicode *composition
  exclusions* — the "same" text can differ in codepoints depending on its
  source. Every comparison in the pipeline normalizes both sides first.
- **Repeatability.** Every stage is incremental and idempotent: unchanged pages
  cost nothing to re-sync and nothing to regenerate, moved pages clean up their
  old files, and the whole site can be rebuilt from the archive offline.

### Built with

- [Eleventy](https://www.11ty.dev/) 3 + the
  [eleventy-notes](https://github.com/rothsandro/eleventy-notes) template
- [Pagefind](https://pagefind.app/) — client-side full-text search (works in Bengali)
- Node.js built-ins only for the pipeline (`fetch`, `node:sqlite`) — zero npm
  dependencies outside the site template
- [Notion API](https://developers.notion.com/) as the content source
- Hosted on Cloudflare Workers and GitHub Pages
- Developed with the help of [Claude Code](https://claude.com/claude-code)

© Dr. Mashiur Rahman. All writing belongs to the author.
