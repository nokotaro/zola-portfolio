# Validation procedure

Read this reference when selecting or signing off validation. Match validation effort to regression risk instead of running a full browser regression for every change.

## Select a level

At task start, identify the changed area, regression risk, and validation level in one or two lines:

```yaml
Validation level: 1
Reason: color-only SCSS change; no layout or interaction changes.
```

### Level 1 — Lightweight validation

Use for small local text or color edits, small non-layout SCSS changes, local semantic HTML fixes, alt or `aria-label` additions, metadata edits, and small JSON, TOML, or Markdown changes.

- Inspect the relevant source and direct consumer.
- Run an appropriate parser or focused source check.
- For site-affecting changes, run `zola check` and `zola build` with the verified baseline.
- Add only checks justified by the change, such as contrast calculation, generated HTML/CSS inspection, or targeted DOM inspection.

Desktop and mobile browser rendering are not required by default.

### Level 2 — Targeted browser validation

Use when changing layout, responsive behavior, breakpoints, JavaScript, fullPage.js behavior, keyboard or focus behavior, modals, search, navigation, overflow, animation, or reduced motion.

Run Level 1, then inspect only the affected viewport, route, and interaction. For example, test an affected breakpoint and adjacent boundary for responsive work, representative desktop keyboard focus for focus work, mobile only for a mobile-only fix, or `/search` only for search work. Check console, broken assets, and overflow when relevant to that behavior.

### Level 3 — Full regression validation

Use only for a grouped audit completion, release or deploy readiness, a large refactor, a cross-cutting layout change, a Zola/fullPage.js/Bulma/jQuery/CMS migration, or an explicit user request.

At minimum, check desktop 1440x900, mobile 390x844, relevant breakpoint boundaries, keyboard navigation, console, search, major routes, broken assets, and horizontal overflow.

## Browser omission rule

Browser rendering may be omitted when layout, responsive behavior, JavaScript, and interaction are unchanged and source or generated HTML/CSS inspection is sufficient. When omitted, state exactly:

`Browser validation: not required for this change`

## Baseline and preflight

1. Run `git status --short --branch` and preserve unrelated changes.
2. For site-affecting work, run `zola --version` and record the result.
3. If the Cloudflare production pin is known, verify and use that version as the baseline.
4. While the production pin is unknown, use Zola 0.18.0 as the verified compatibility baseline.
5. Do not use a different local Zola version as a substitute for baseline validation.
6. When possible, obtain or select Zola 0.18.0 and run the required checks with it.
7. If the baseline version is unavailable, continue source inspection and safe, in-scope editing where possible, but record that baseline Zola validation was not performed.
8. Do not migrate `config.toml` or other production inputs merely to resolve a local version mismatch unless migration is the requested task.

## Required site checks

For any change to production inputs (`config.toml`, templates, Sass, content, data, static assets, or CMS/deployment configuration), run:

```bash
zola check
zola build
```

`zola check` includes link checks and reports content issues. `zola build` verifies generation and Sass/template compilation. Record warnings such as orphan pages rather than hiding them.

To avoid mixing generated files with source during an audit, an explicit temporary output directory is acceptable when supported:

```bash
zola build --output-dir <temporary-directory>
```

## Targeted checks

| Change | Additional validation |
| --- | --- |
| JSON data | Parse edited JSON; inspect its direct template consumer |
| TOML/config | Parse through the target Zola version; inspect every affected `config.extra` consumer |
| Markdown/front matter | Inspect the generated page or section when source alone is insufficient |
| CMS YAML | Parse YAML when tooling is available; compare fields with front matter and templates |
| Tera | Exercise every branch affected by flags or optional values, not only the active production branch |
| SCSS/CSS | Inspect compiled `main.css`; use a browser only for affected visual or layout behavior |
| JavaScript | Exercise the interaction, inspect console errors, and check keyboard behavior |
| Search | Open `/search`, enter representative text, and verify result links |
| Tags | Inspect tag list/single pages and feed link generation |
| Feed/SEO | Inspect generated head markup, feed files, sitemap, and robots output |
| Publications/BibTeX | Enable only in an isolated test configuration if necessary; test modal, fetch, copy, and focus behavior |

## Level 2 browser sign-off

Serve the local build with the validated Zola version and check only what the changed behavior requires:

- Use only the affected viewport and breakpoint boundary.
- Exercise only the affected route, control, and interaction.
- Inspect console, missing assets, clipping, or overflow when the change could affect them.
- Use keyboard navigation when keyboard or focus behavior is affected.
- Do not add unrelated routes or viewports merely to make the check look comprehensive.

Use a Cloudflare preview only when the user asks for deployment/preview work or the local environment cannot reproduce a provider-specific issue. Never treat a local build as proof of a successful production deployment.

## Grouped regression

Small audit fixes such as AUD-03, AUD-04, the remaining AUD-02 work, and AUD-07 may use Level 1 or focused Level 2 validation independently. After several related tasks reach a meaningful completion boundary, run one Level 3 regression instead of repeating it for every fix.

## Context and token discipline

- Do not reread the full repository for each task.
- Load required instruction files once, apply only their task-relevant guidance, and open only task-specific references.
- Reuse established architecture knowledge unless the changed area or repository state requires reconfirmation.
- Avoid large screenshot collections and full DOM dumps; gather the smallest evidence that verifies the behavior.
- Keep completion reports concise and do not repeatedly enumerate known baseline issues that did not affect the result.

## Instruction-only changes

When only `AGENTS.md` or `.agents/skills/` changes:

- Run the Skill Creator `quick_validate.py` against each changed skill.
- Check links from `SKILL.md` to references.
- Run `git diff --check` and confirm the final `git status --short`.
- Do not run Zola or browser validation unless the instruction change itself requires it.

## Release report

Report the selected level, Zola version and commands for site validation, focused browser coverage when used, material limitations, and whether preview or production was touched. Keep known baseline issues brief unless they changed the outcome.
