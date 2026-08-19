# Purelane Shopify Assignment

## What I changed

The static Purelane HTML prototype was converted into a fully functional Shopify 2.0 theme built on top of Dawn. Each visual section from the prototype was decomposed into a reusable Liquid section with its own scoped CSS and vanilla JavaScript.

### Sections implemented

| Section | Files |
|---------|-------|
| **Curved Navigation / Header** | `sections/purelane-header.liquid`, `assets/purelane-header.css` |
| **Hero** | `sections/purelane-hero.liquid`, `assets/purelane-hero.css` |
| **Reviews Rail** | `sections/purelane-reviews.liquid`, `snippets/purelane-review-card.liquid`, `assets/purelane-reviews.css` |
| **Best-selling Combos** | `sections/purelane-combos.liquid`, `snippets/purelane-combo-card.liquid`, `assets/purelane-combos.css` |
| **Bundles** | `sections/purelane-bundles.liquid`, `snippets/purelane-tier-card.liquid`, `assets/purelane-bundles.css` |
| **Shop / Product Grid** | `sections/purelane-shop.liquid`, `snippets/purelane-product-card.liquid`, `assets/purelane-shop.css` |
| **Footer + Sticky CTA** | `sections/purelane-footer.liquid`, `assets/purelane-footer.css` |

### Configuration files modified

- `sections/header-group.json` — replaced Dawn header with `purelane-header`
- `sections/footer-group.json` — replaced Dawn footer with `purelane-footer`
- `templates/index.json` — configured homepage section order (Hero → Reviews → Combos → Bundles → Shop)

### Key features

- **Dynamic curved glass navigation** — floating pill-shaped header with scroll-triggered elevation transition, mobile drawer with Escape key dismiss, and side progress rail
- **Product data from Shopify** — Shop grid pulls products from a Shopify collection, handles sold-out badges, missing images, and long titles
- **Responsive layout** — tested across 375px to 1440px viewports with no horizontal overflow
- **Merchant editability** — all sections expose settings in the Shopify Theme Editor (headings, CTAs, pricing, collection picker, animation toggles)
- **Accessibility** — semantic HTML, alt text, aria labels, keyboard focus states, mobile drawer keyboard trapping, `prefers-reduced-motion` support across all animated sections

---

## What I changed from the original prototype

| Prototype limitation | Shopify implementation |
|---------------------|----------------------|
| Static HTML with hardcoded content | Shopify-native Liquid with dynamic product data, collection bindings, and `{{ shop.name }}` |
| Single monolithic file | 7 independent reusable sections + 4 snippets, each with its own CSS |
| No CMS or editor support | Full Shopify Theme Editor compatibility with schema settings and blocks |
| No accessibility features | Semantic headings (h1–h5), ARIA labels, focus-visible rings, `prefers-reduced-motion`, keyboard navigation, Escape key drawer dismiss |
| Fixed content only | Handles edge cases: sold-out products, missing product images (SVG fallback), long titles (word-break), and empty collections |
| Desktop-first styling | Responsive CSS Grid and Flexbox with mobile-first breakpoints at 760px, 960px, and 1180px |
| No Shopify data binding | Products, pricing, collection names, cart count, and account URLs all come from Shopify Liquid objects |
| No section isolation | All sections use `section.id`-scoped IDs and class names, safe for Theme Editor duplication and reordering |

---

## What I would do with more time

1. **Automated visual regression testing** — Set up Percy or BackstopJS to capture reference screenshots and diff against the Shopify render on every push
2. **Core Web Vitals optimization** — Move Google Fonts to `layout/theme.liquid` with `font-display: swap` preloading, add `fetchpriority="high"` to hero LCP image, and investigate lazy-loading the reviews marquee below the fold
3. **Additional device and browser testing** — Cross-browser testing on Safari iOS, Samsung Internet, and older Chrome versions; verify touch interactions on actual physical devices
4. **Richer merchandising data** — Replace hardcoded combo/bundle pricing with Shopify metafields or metaobjects so merchants can manage bundle configurations entirely through the admin

---

## AI workflow

1. **Inspected original HTML/CSS/JS** — Read the complete `reference/purelane-homepage.html` to identify every section, its CSS variables, typography, spacing, animations, and responsive breakpoints
2. **Decomposed the prototype into Shopify sections** — Mapped each `<section>` in the reference to a separate `.liquid` file with its own CSS asset and schema
3. **Used AI agents for implementation** — Used targeted prompts to generate each section's Liquid, CSS, and JavaScript; iterated through multiple rounds per section
4. **Rendered and compared output** — Used `shopify theme dev` to preview locally at `http://127.0.0.1:9292` and visually compared against the reference HTML
5. **Used targeted prompts to fix visual differences** — Identified spacing, color, and layout mismatches by side-by-side inspection and issued specific correction prompts
6. **Ran Theme Check** — Used `shopify theme check` to verify Liquid syntax, schema validity, and best practices compliance
7. **Performed responsive/accessibility QA** — Verified keyboard navigation, focus states, screen reader semantics, and responsive behavior across multiple viewport widths

Human review was required throughout for visual comparison, edge case identification, and verifying that the Shopify implementation faithfully reproduced the prototype's design intent rather than just its HTML structure.
