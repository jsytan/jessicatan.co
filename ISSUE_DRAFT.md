## Context

Build the `jessicatan.co` one-pager in Astro. Content and design tokens are already locked and committed:

- `src/content/site.json` — every word on the page, final
- `src/styles/tokens.css` — colours, type, spacing, all as CSS custom properties
- Design reference (visual source of truth for layout/spacing/hierarchy): https://claude.ai/code/artifact/8ee46140-9892-423c-a1c3-b45a5e2f30f2

Read the artifact link first — it shows exactly what each section should look like at desktop and mobile widths. Palette: ink `#16213e`, accent blue `#22409e`, paper `#ffffff`/`#f3f5fa`, body `#5b6472`. Type: Lora (display/serif) + Inter (body). Both loaded from Google Fonts in the reference — same `<link>` approach works in Astro's `<head>`.

## What to build

`src/layouts/BaseLayout.astro`
- `<head>` with title/description from `site.json` meta, Open Graph tags, canonical URL, font preconnect + stylesheet links
- Wraps Header + page content + Footer
- `@media (prefers-reduced-motion: no-preference) { html { scroll-behavior: smooth; } }` — no other motion

`src/components/Header.astro` — wordmark + nav (Expertise / Experience / Contact), matches reference

`src/components/Hero.astro` — eyebrow, headline, intro paragraph, location, reads from `hero` in site.json

`src/components/Expertise.astro` — "How I help", 3-column cards with the blue top-rule, reads `expertise.items`

`src/components/Impact.astro` — "Selected impact", 2×2 bordered tile grid, reads `impact.items` (exactly 4 — don't add more without checking with Jess first)

`src/components/Experience.astro` — two-row company treatment, this is the part needing the most care:
- Primary row (4 items, from `experience.companiesPrimary`): company logo + one title line each. AVIV's entry also has a smaller "Part of Axel Springer" line/logo underneath — see `parent`/`parentLogo` fields.
- Secondary row (from `experience.companiesSecondary`): logos only, no title text, visually lighter than the primary row.
- **Logos are not sourced yet.** `site.json` references paths like `/logos/aviv.svg` that don't exist. Source each company's official logo (their press/brand kit page is usually the safe source — monochrome/wordmark version if available) and drop them into `public/logos/`. Until then, fall back to styled text (company name in Lora, as in the reference) rather than a broken image.
- Venture callout: reads `venture`, styled as a quiet inset box (left border, tinted background), not a full section.

`src/components/Contact.astro` — dark full-bleed closing band, headline + sub + email button (solid) + LinkedIn button (icon-only — inline SVG in the reference, swap for a real icon if you have a preferred icon set)

`src/components/Footer.astro` — minimal, brand + **dynamically computed year** (`new Date().getFullYear()`, not hardcoded)

`src/pages/index.astro` — composes all of the above in order: Hero, Expertise, Impact, Experience, Contact, Footer

## Constraints

- Don't invent or round any numbers in `impact.items` or `experience` — they've already been checked against Jess's CV. If something looks like it should be different, ask rather than change it.
- Responsive breakpoints at 900px and 520px (see reference CSS for exact behavior at each).
- Keyboard focus must be visible (`:focus-visible` outline, accent colour).
- No image, icon, or company mark beyond what's listed in `site.json` — don't add client logos, testimonials, or additional sections.
- `npx astro add sitemap` is already done; wire the meta/OG tags to actually use it.

## Acceptance criteria

- [ ] Matches the reference artifact's layout, spacing, and hierarchy at desktop and mobile widths
- [ ] All content comes from `site.json` — no hardcoded copy in components
- [ ] All colours/type/spacing come from `tokens.css` — no hardcoded hex values in components
- [ ] `npm run build` succeeds with no errors
- [ ] Keyboard navigation works end to end, focus states visible
- [ ] `prefers-reduced-motion` respected
- [ ] Company logos either real (sourced from official brand assets) or a clean text fallback — never a broken image
