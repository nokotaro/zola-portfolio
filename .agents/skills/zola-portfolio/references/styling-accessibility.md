# Styling, responsive behavior, and accessibility

Read this reference before changing templates, `sass/main.scss`, or browser-side layout behavior.

## Styling architecture

- `sass/main.scss` is the only local Sass file; there are no partials, variables, mixins, CSS modules, or build-time frontend dependencies.
- Bulma 0.9.3 supplies layout and visibility utilities. Local CSS loads after Bulma and can override it.
- Templates contain substantial legacy inline styles. Do not add more automatically; first decide whether a small local rule is clearer and consistent with the existing single-file structure.
- Brand colors currently use `#82ae46` and darker interactive green `#465e25`.

## Current responsive behavior

- Desktop uses a fixed 17rem sidebar and offsets `#content` at the local Sass breakpoint `min-width: 992px`.
- The sidebar also has Bulma's `is-hidden-touch` class.
- fullPage.js disables auto-scrolling below 936px; at larger widths it uses section anchors and viewport paging.
- Each homepage `.page` is `100vh` with `overflow: hidden`; inner containers provide their own overflow where needed.
- On touch/mobile layouts the sidebar and the About achievements/social block are hidden, while the sections remain stacked at viewport height and use normal scrolling.
- The 936px JavaScript threshold, 992px Sass breakpoint, and Bulma visibility breakpoint are separate contracts. Test the boundary region whenever navigation, sizing, or visibility changes.

Representative regression viewports:

- Mobile: 390x844.
- Desktop: 1440x900.
- Boundary checks: widths around 935/936 and 991/992, plus the effective Bulma touch cutoff.

Check horizontal overflow, clipped content inside `100vh` sections, sidebar/content overlap or blank offsets, anchor navigation, and whether all content remains reachable with zoomed or enlarged text.

## Current accessibility assumptions

The current site has useful native links and headings but is not an established WCAG-conformant baseline. Audit observations on 2026-08-26 include:

- The document declares `lang="en"`.
- There is no `main` landmark.
- The avatar and achievement badge lack `alt`; several icon-only social links rely on `title` rather than an explicit accessible name.
- Heading levels reflect visual styling more than a strict document outline in experience and education sections.
- The BibTeX modal is always present in the DOM and its trigger/close/focus behavior requires dedicated keyboard testing when publications are enabled.
- fullPage.js movement, fixed viewport sections, and hidden overflow can affect zoom, keyboard navigation, and reduced-motion expectations.
- The current public site logs fullPage.js 3 license-key errors in the browser console. Treat them as a known baseline signal; do not attribute them to a new change unless the error set changes.

These are pre-existing observations, not authorization to change the design. Prevent regressions in ordinary work and address baseline issues only when requested.

## Validation expectations

For user-visible changes, verify:

- Semantic element and accessible-name behavior.
- Keyboard-only access, visible focus, and logical focus order.
- Image alternatives appropriate to content versus decoration.
- Text and interactive-state contrast.
- Reflow and text reachability at mobile width and browser zoom.
- `prefers-reduced-motion` impact when animation or scrolling behavior changes.
- No new console errors, missing CDN assets, or broken external/local images.

Automated checks can find only a subset of accessibility defects. Combine them with DOM inspection and keyboard/visual checks.

## SEO and discovery baseline

`config.toml` provides a title and description, but the current base template renders only the `<title>` and RSS link; it does not emit a meta description, canonical URL, Open Graph, or structured data. Search, tag pages, sitemap, robots, and legacy feed configuration are Zola-generated features. Preserve existing output unless SEO work is explicitly requested.
