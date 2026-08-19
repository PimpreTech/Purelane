# Purelane Shopify Assignment — Final QA Report

**Date**: 2026-08-19
**Tester**: AI-assisted QA with human review required

---

## 1. Theme Check

```
166 files inspected
0 errors
14 warnings (all in Dawn base files, not custom Purelane code)
```

**Warnings breakdown**:
- `layout/password.liquid` — UndefinedObject `scheme_classes` (Dawn base)
- `layout/theme.liquid` — UndefinedObject `scheme_classes` (Dawn base)
- `sections/main-article.liquid` — VariableName `anchorId` (Dawn base)
- `sections/main-list-collections.liquid` — VariableName `moduloResult` (Dawn base)
- `sections/main-product.liquid` — UnusedAssign `seo_media`, UndefinedObject `continue` (Dawn base)
- `sections/main-search.liquid` — UnusedAssign `product_settings` (Dawn base)
- `sections/purelane-hero.liquid` — 3× RemoteAsset for Google Fonts (acceptable; Outfit + Inter required for visual fidelity)
- `snippets/header-drawer.liquid` — 3× UndefinedObject `section` (Dawn base snippet, not used by our header)
- `snippets/quick-order-product-row.liquid` — OrphanedSnippet (Dawn base)

**Result: PASS** (0 errors)

---

## 2. Theme Structure Verification

### Required custom sections

| File | Exists | Referenced in config |
|------|:------:|:-------------------:|
| `sections/purelane-header.liquid` | ✅ | `header-group.json` |
| `sections/purelane-hero.liquid` | ✅ | `index.json` |
| `sections/purelane-reviews.liquid` | ✅ | `index.json` |
| `sections/purelane-combos.liquid` | ✅ | `index.json` |
| `sections/purelane-bundles.liquid` | ✅ | `index.json` |
| `sections/purelane-shop.liquid` | ✅ | `index.json` |
| `sections/purelane-footer.liquid` | ✅ | `footer-group.json` |

### Required custom snippets

| File | Exists |
|------|:------:|
| `snippets/purelane-product-card.liquid` | ✅ |
| `snippets/purelane-combo-card.liquid` | ✅ |
| `snippets/purelane-tier-card.liquid` | ✅ |
| `snippets/purelane-review-card.liquid` | ✅ |

### Required CSS assets

| File | Exists |
|------|:------:|
| `assets/purelane-header.css` | ✅ |
| `assets/purelane-hero.css` | ✅ |
| `assets/purelane-reviews.css` | ✅ |
| `assets/purelane-combos.css` | ✅ |
| `assets/purelane-bundles.css` | ✅ |
| `assets/purelane-shop.css` | ✅ |
| `assets/purelane-footer.css` | ✅ |

### Homepage section order (index.json)

1. `purelane_hero` → Hero
2. `purelane_reviews` → Reviews Rail
3. `purelane_combos` → Best-selling Combos
4. `purelane_bundles` → Bundles
5. `purelane_shop` → Shop / Product Grid

This matches the reference prototype order (Hero → Reviews → Combos → Bundles → Shop).

**Result: PASS**

---

## 3. Full Page QA

All sections verified to exist and render in the theme dev server. Visual comparison performed against reference HTML during development.

- **Header**: Curved glass navpill, brand mark, navigation links, search/account/cart icons, mobile burger with drawer, progress rail
- **Hero**: 3-line headline, floral divider, lede text, CTA buttons, badge strip, product slider with pricing
- **Reviews**: Infinite marquee rail with review cards, aggregate rating pill, hover/focus pause
- **Combos**: Horizontal scroll-snap rail with combo cards, benefit stacks, pricing, featured card
- **Bundles**: Header panel + 3-tier pricing grid with feature checklists
- **Shop**: 4-column grid (2-column mobile) from Shopify collection data
- **Footer**: 4-column glass layout, copyright bar, mobile sticky CTA

**Result: PASS**

---

## 4. Responsive QA

Verified via code inspection of CSS breakpoints across all section stylesheets.

| Viewport | Breakpoints verified |
|----------|---------------------|
| 375px | Mobile layout, single-column stacks, 2-column shop grid |
| 390px | Same as 375px |
| 430px | Same as 375px |
| 768px | Tablet breakpoint at 760px, grid transitions |
| 1024px | Desktop layouts activate |
| 1280px | Full desktop, 4-column shop grid |
| 1440px | Max-width containers center content |

CSS features verified:
- No fixed pixel widths that would cause horizontal overflow
- `overflow-x: hidden` or `overflow: hidden` on scroll containers
- `word-break: break-word` on product titles
- Mobile sticky CTA hidden on desktop (≥960px)
- Progress rail hidden on mobile (<1180px)

**Result: PASS** (code inspection; not manually verified on physical devices at each width)

---

## 5. Shopify Data Verification

### Product data binding
- Shop section uses `section.settings.collection` with fallback to `collections.all`
- Products loop via `collection.products limit: products_to_show`

### Required edge cases

| Edge case | Implementation |
|-----------|---------------|
| **Sold-out product** | `unless card_product.available` → "Sold out" pill badge, disabled button with `aria-disabled="true"`, muted styling |
| **No product image** | Falls back to inline SVG bottle illustration with product-ID-scoped gradient IDs |
| **Long product title** | CSS `word-break: break-word; overflow-wrap: break-word; line-height: 1.2` with flexbox `margin-top: auto` for bottom alignment |
| **8+ products** | `products_to_show` range setting supports 2–24 products (step 2) |

**Result: PASS** (code verified; actual edge case rendering depends on store product data)

---

## 6. Theme Editor Compatibility

### Schema verification

| Section | Settings | Blocks | Editor events |
|---------|:--------:|:------:|:------------:|
| Header | ✅ menu, ticker, progress rail | — | — |
| Hero | ✅ headings, CTAs, badges, pricing, image, animations | — | `shopify:section:load` ✅ |
| Reviews | ✅ kicker, rating, homes stat, animations | ✅ review blocks | `shopify:section:load` ✅ |
| Combos | ✅ kicker, title, lede, animations | ✅ combo blocks | `shopify:section:load` ✅ |
| Bundles | ✅ kicker, title, lede, animations | ✅ tier blocks | `shopify:section:load` ✅ |
| Shop | ✅ kicker, title, collection, products_to_show, rating, animations | — | `shopify:section:load` ✅ |
| Footer | ✅ sticky CTA settings | — | — |

### Section isolation
All sections use `section.id`-scoped container IDs and JavaScript selectors. Safe for duplication and reordering.

**Result: PASS** (schema verified via code inspection; not manually tested in Shopify admin theme editor)

---

## 7. Merchant Editability

| Content type | Editable via | Notes |
|-------------|-------------|-------|
| Hero headline, lede, CTAs | Section settings | 3-line headline, primary/secondary CTA text + URL |
| Hero badges | Section settings | 3 configurable trust badges |
| Hero product slider | Section settings | Price, compare-at, savings label per slide |
| Shop collection | Section settings | Collection picker + product count slider |
| Combo cards | Blocks | Title, pricing, benefits, CTA, product picker |
| Bundle tiers | Blocks | Tag, quantity, pricing, features, CTA |
| Review cards | Blocks | Title, text, author, product tag, stars, verified badge |
| Footer sticky CTA | Section settings | Heading, subtext, button text, URL |
| Navigation menu | Section settings | link_list picker |
| Animation toggles | Section settings | Per-section enable/disable |

**Known gap**: Header ticker text and footer navigation links are currently hardcoded. These could be converted to blocks or settings with more time.

**Result: PASS**

---

## 8. Accessibility Verification

| Feature | Status | Implementation |
|---------|:------:|---------------|
| Semantic headings | ✅ | h1 (hero), h2 (section titles), h3 (cards), h5 (reviews, footer) |
| Image alt text | ✅ | Dynamic alt from product media with title fallback |
| Keyboard navigation | ✅ | All controls are native `<a>` and `<button>` elements |
| Focus-visible states | ✅ | `outline: 2px solid #4f7d10; outline-offset: 3px` across all sections |
| Accessible buttons | ✅ | Proper `type="submit"`, `aria-disabled="true"` when sold out |
| Mobile drawer keyboard | ✅ | Escape key dismisses drawer, focus returns to burger button |
| aria-label / aria-expanded | ✅ | Burger toggle, nav landmarks, scroll regions, icon buttons |
| aria-hidden on decorative elements | ✅ | SVGs, dividers, checkmarks |
| prefers-reduced-motion | ✅ | Hero, reviews marquee, combos, bundles, header ticker, footer |
| Color contrast | ⚠️ | Not manually verified with contrast checker tools |

**Result: PASS** (code-level verification; manual contrast checking recommended)

---

## 9. Performance Observations

| Aspect | Status | Notes |
|--------|:------:|-------|
| External JS libraries | ✅ None | Zero jQuery, Swiper, GSAP, or other dependencies |
| JavaScript weight | ✅ Minimal | ~30 lines vanilla JS per section, IIFE-wrapped |
| Image optimization | ✅ | Shopify `image_url` + `image_tag` with responsive widths and lazy loading |
| CSS architecture | ✅ | Section-scoped stylesheets, no global CSS pollution |
| Render blocking | ⚠️ | Google Fonts in hero section body (could be moved to `<head>`) |
| DOM complexity | ✅ | Reasonable element count, no excessive nesting |

**Result: PASS** (minor Google Fonts placement optimization possible)

---

## 10. Git Status

```
Branch: main
Remote: origin → https://github.com/Shopify/dawn.git

Modified files: 3
  - sections/footer-group.json
  - sections/header-group.json
  - templates/index.json

New files: 18
  - 7 CSS assets (purelane-*.css)
  - 7 liquid sections (purelane-*.liquid)
  - 4 liquid snippets (purelane-*.liquid)
```

**Latest commit**: `d8a8f4d4 Complete Purelane Shopify assignment`

**Result**: UNCOMMITTED CHANGES (commit prepared)

---

## 11. Remaining Known Limitations

1. **Header ticker text is hardcoded** — Merchant cannot edit announcement messages without code changes
2. **Footer navigation links are hardcoded** — Could be converted to link_list settings
3. **Google Fonts loaded from section body** — Should ideally be in `layout/theme.liquid` `<head>` for better FOUC prevention
4. **Progress rail uses hardcoded section anchors** — Anchors are statically named and may not match dynamically-generated Shopify section IDs in all configurations
5. **Remote origin points to Shopify/dawn** — Needs to be updated to your own GitHub repository before push
6. **Color contrast not instrument-verified** — WCAG contrast ratios should be manually checked with a tool like axe or Lighthouse

---

## 12. Items Requiring Manual Verification Before Submission

1. **Fill in SUBMISSION_INFO.md** — Add your dev store password and GitHub repository URL
2. **Update git remote** — Change `origin` from `https://github.com/Shopify/dawn.git` to your own repository
3. **Push to GitHub** — Run `git push` after setting the correct remote
4. **Test in Shopify admin** — Open the theme editor and verify settings/blocks are editable
5. **Test on the live preview URL** — Verify `https://purelane-troopod-le6trmjj.myshopify.com/?preview_theme_id=160026624199` renders correctly
6. **Create test products** — Ensure at least one sold-out product, one without an image, and one with a very long title exist in the store
7. **Cross-browser test** — Test on Safari, Firefox, and mobile Chrome
