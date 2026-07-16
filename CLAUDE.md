# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository overview

This repo is a static marketing site: [index.html](index.html) (the main landing page), [terms.html](terms.html) (Terms of Service), [privacy.html](privacy.html) (Privacy Policy), [refund.html](refund.html) (Refund Policy), plus a [static/](static/) folder of image assets. There is no build system, package manager, test suite, or server code — each page is a self-contained HTML file with its own inline `<style>` block and no JavaScript.

To preview a page, just open it directly in a browser (or serve the directory with any static file server). This site is deployed to **GoDaddy hosting via a GitHub-linked deploy**, which serves files as-is over Apache with no clean-URL rewriting — so all internal links use plain filenames (`terms.html`, `privacy.html`, `refund.html`), not extension-less paths like `/terms`. Keep new pages linked the same way unless a `.htaccess` rewrite is added.

**`terms.html`, `privacy.html`, and `refund.html` are placeholder boilerplate** — none has been reviewed by a lawyer and must not be treated as vetted legal text, even though the on-page disclaimer banner that used to say so has been removed at the site owner's request. `privacy.html` names Paddle as the payment processor/merchant of record and includes CCPA-specific rights language (US + California scope); `refund.html` states a no-refunds/cancel-anytime policy and deliberately does not name Paddle. `terms.html`'s billing section links to `refund.html` — keep that cross-reference in sync if the refund stance changes.

## Structure of index.html

The file is organized top to bottom as:

1. **`<head>`** — SEO metadata (title, description, keywords, Open Graph, Twitter cards), plus two `application/ld+json` blocks: a `SoftwareApplication` schema (product/pricing) and a `FAQPage` schema. **These JSON-LD blocks duplicate content that also appears in the visible page** (pricing tiers in the Pricing section, FAQ text in the FAQ section) — when editing pricing or FAQ copy, update both the visible HTML and the corresponding JSON-LD block so they stay in sync.
2. **`<style>`** — all CSS lives inline in one block, using CSS custom properties defined on `:root` (colors, fonts, radius, shadow). Dark mode is handled via `@media (prefers-color-scheme: dark)` overriding the same custom properties — there is no separate dark-mode stylesheet or class toggle.
3. **`<body>`** — semantic sections in order: header/nav → hero → trust strip → features → how-it-works → value band → pricing → FAQ (using native `<details>`/`<summary>`, no JS) → final CTA → footer.

`terms.html`, `privacy.html`, and `refund.html` reuse the same header/footer markup and CSS custom properties as `index.html`, plus a shared `.legal` content style (max-width text column, numbered `<h2>` sections, a `.disclaimer` callout box). Their nav links to `index.html`'s sections use `/#anchor` (root path + fragment) since those sections don't exist on the legal pages themselves — root-relative `/` and `/#anchor` are safe even without clean-URL rewriting, since every static host serves `index.html` at `/` by default; it's only *deeper* extension-less paths (like the earlier `/terms`) that need a rewrite rule and were changed to plain filenames instead.

## Conventions to preserve when editing

- Keep pricing figures, plan names, and unit caps consistent across three places: the `SoftwareApplication` JSON-LD `offers` array, the visible `.pricing-grid` cards, and any mention elsewhere in copy (e.g. FAQ answers, `pricing-note`).
- Keep FAQ question/answer text consistent across the `FAQPage` JSON-LD `mainEntity` array and the visible `.faq-list` `<details>` items.
- The favicon and in-page `<img>` tags reference `static/icon.png`, `static/logo.png`, `static/logo-dark.png` as paths **relative** to each HTML file (not root-absolute) so pages render correctly both opened directly via `file://` and when deployed with `static/` as a sibling of the HTML files. The `og:image`/`twitter:image` meta tags are the one exception and stay root-absolute (`/static/logo.png`), since social crawlers resolve those against the deployed domain, not the local file.
- All internal links to app functionality (Log In, Get Started, Choose Plan) point to `https://tenantrack-257544703838.us-west2.run.app/`, the actual application, which is a separate deployment (Cloud Run) from this marketing site, and open in a new tab (`target="_blank" rel="noopener"`). Don't confuse these with the brand-logo/homepage links (`/`, `index.html`), which stay within this site and open in the same tab.
- The copyright line in both footers reads "Anurag Prem Soni," not a company name, since the business isn't a registered legal entity — update this if that changes.
- Responsive breakpoints are `880px` and `560px`, handled via plain media queries at the bottom of each page's `<style>` block — no CSS framework or preprocessor is used.
