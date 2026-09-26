# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static marketing site (Czech language, `lang="cs"`) for "Výkup domů 24", a real-estate purchasing company. No build system, no package manager, no framework — plain HTML/CSS/JS served as static files. Deployed on Vercel (custom domain `vykupdomu24.cz`) with no `vercel.json` — Vercel serves the static files as-is with no build step.

## Development

There is no build/lint/test tooling in this repo. To preview changes, just open the HTML files directly in a browser or serve the directory statically, e.g.:

```
python3 -m http.server 8000
```

There are no automated tests. Verify changes by opening the affected page(s) in a browser.

## Structure

- `index.html` — homepage, contains most of the sections (`#vykup`, `#projekty`, `#kontakt`) that other pages link to as in-page anchors.
- `vykup/*.html` — one landing page per property type (`byty`, `domy`, `pozemky`, `exekuce`, `zadluzené`), each a near-duplicate of the homepage template with copy tailored to that property type.
- `zastava.html`, `investice.html`, `soukromi.html`, `projekty.html`, `404.html` — standalone pages, each duplicating the same `<head>`/header/footer boilerplate.
- `styles.css` — single global stylesheet for the whole site, organized into commented sections (Header, Hero, Trust stats, Buttons, Property Types, Process timeline, Contact Form, Footer, Projects page, Invest page, Cookie bar, etc.). Add new styles under the matching section rather than appending at the end.
- `script.js` — single global script, structured as independent `init*()` functions called from one `DOMContentLoaded` listener. Each feature (nav burger, trust-stat counters, contact form submission, project lightbox gallery, cookie consent bar) is self-contained and no-ops if its expected DOM elements aren't present on the current page — this is what lets one script file serve every page safely.
- `img/` — static assets, including per-page OG images (`og-img-<page>.jpg`) and `img/projects/` for the project gallery.
- `sitemap.xml`, `robots.txt` — kept in sync manually when pages are added/removed.

## Conventions

- Every page repeats the same `<head>` boilerplate: Google tag (gtag.js) with default-denied consent, full OG/Twitter meta tags, canonical URL, favicon links, Google Fonts (DM Sans + Outfit), and a `LocalBusiness` JSON-LD block on `index.html`. When adding a new page, copy this boilerplate from an existing page and update the title, description, OG image, and canonical URL.
- Analytics consent is denied by default and only granted via the cookie bar (`initCookieBar` in `script.js`), which calls `gtag('consent', 'update', ...)` on accept. Don't bypass this consent gate when adding tracking.
- The contact form (`#contactForm`) submits to Formspree (`https://formspree.io/f/xlgppbvr`) via `fetch`, with client-side required-field validation and inline button state feedback (no page reload).
- Navigation is shared markup duplicated across all pages (logo, burger menu, nav links, phone CTA); `initNavBurger()` in `script.js` drives the mobile menu and expects the `.nav-burger`/`.nav`/`.header`/`.nav-overlay` classes to exist exactly as in the header markup.
