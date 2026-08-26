# Repository guidance

## Scope and architecture

- This repository is a Zola static site, not a Rust application. It has no `Cargo.toml` or Rust source tree.
- The editable stack is Zola configuration (TOML), Tera templates, Markdown content, JSON data, SCSS/CSS, small browser-side JavaScript snippets, and CMS/deployment YAML.
- Use the repository-local `zola-portfolio` skill for changes or reviews involving the site, its content model, templates, styling, validation, or deployment configuration. Read only the skill references relevant to the task.
- Production is built and deployed with Cloudflare Workers & Pages and is served at `https://about.nokotaro.com/`. Deployment is not implied by a request to edit or validate files.

## Change discipline

- Make only the change the user requested. Do not perform unrelated cleanup, broad refactors, directory reorganizations, dependency upgrades, content rewrites, or design changes.
- Preserve the existing one-page layout, fullPage.js navigation, responsive behavior, section visibility, generated URLs, and content meaning unless the request explicitly changes them.
- Do not add or replace external libraries without explaining the need, checking existing capabilities, and obtaining user direction when the choice affects production behavior.
- Treat names, employment, education, contact details, social accounts, achievements, and other personal information as user-owned facts. Never infer, add, translate, or “correct” them without explicit evidence or instruction.
- Preserve unrelated user changes in a dirty worktree. Before editing, inspect `git status` and the files that consume the values being changed.

## Zola and Tera

- Use syntax supported by the repository's verified Zola compatibility baseline. Zola 0.18.0 currently passes `zola check` and `zola build`. Zola 0.19.2, 0.21.0, and 0.23.4 were tested and reject the current `generate_feed` setting. Do not infer results for untested Zola versions, and do not silently migrate Zola or configuration syntax as part of another task.
- If the Cloudflare production `ZOLA_VERSION` setting becomes available, verify it separately, treat that pinned production version as authoritative, and update the documented baseline deliberately.
- Do not assume a Jinja2 feature exists in Tera. Confirm uncertain filters, tests, functions, scoping, or inheritance behavior against the Zola/Tera version in use.
- Preserve the existing inheritance and composition model: templates extend `base.html`; shared rendering belongs in existing macros or partials; avoid duplicating markup when an existing macro or partial is the intended owner.
- Keep the semantics of `config.extra` flags and values stable. When adding or changing one, inspect every template and CMS field that consumes it.
- Be deliberate with `| safe`, `markdown()`, `load_data()`, `get_section()`, `get_url()`, and `set_global`; these are rendering and escaping boundaries, not interchangeable helpers.

## Content and data

- Keep the roles distinct: `content/` holds Markdown sections/pages and front matter; `data/` holds structured homepage data; `static/` is copied through to output; `templates/` consumes all three.
- Before changing front matter or a JSON shape, inspect the consumer template and `static/admin/config.yml`. Update all coupled schemas only when the request requires it.
- Preserve section `_index.md` files and the established section names (`blog`, `projects`, `opensource`, and `publications`).
- Keep JSON valid and retain the existing mixed skill representation (string or object) unless a coordinated schema migration is explicitly requested.

## Styling, responsive behavior, and accessibility

- Inspect `sass/main.scss`, Bulma utilities, template markup, and existing inline styles before adding a new rule. Prefer the smallest change in the existing Sass entrypoint.
- Validate only the viewports and interactions required by the selected validation level. Test both desktop and mobile when both are affected, for a Level 3 regression, or when the user requests it. Pay special attention to the 936px fullPage.js threshold, the 992px Sass breakpoint, Bulma's touch visibility helpers, fixed `100vh` sections, and overflow behavior when those contracts are affected.
- Do not worsen semantic structure, keyboard access, focus visibility, alternative text, link/button names, color contrast, reduced-motion behavior, or zoom/reflow. Do not add ARIA when native HTML already provides the correct semantics.
- Accessibility improvements that change current markup or behavior require an explicit request; record discovered pre-existing issues without folding them into unrelated work.

## Validation

- At the start of a task, state the changed area, regression risk, and validation level in one or two lines. Use the detailed criteria in `.agents/skills/zola-portfolio/references/validation.md`.
- Use Level 1 for small, local source/content/style changes; Level 2 for affected layout, responsive, JavaScript, navigation, keyboard, focus, search, modal, overflow, or animation behavior; and Level 3 only for release/deploy readiness, migrations, large or cross-cutting changes, grouped audit completion, or an explicit user request.
- For any site-affecting change, run at minimum:

  ```bash
  zola check
  zola build
  ```

- Always report the Zola version used for site validation.
- Use the production-pinned Zola version when known. While that pin is unknown, use Zola 0.18.0 as the verified compatibility baseline; do not substitute a different local version or claim compatibility with an untested release.
- A local version mismatch does not prohibit source inspection or otherwise safe, in-scope editing. If possible, obtain or use Zola 0.18.0 for `zola check` and `zola build`. If the baseline version is unavailable, continue safe work, do not migrate `config.toml` merely to satisfy the installed version, and state clearly in the completion report that baseline Zola validation was not performed.
- Validate edited JSON, TOML, YAML, HTML, CSS, or JavaScript with an appropriate parser or targeted check when available.
- Browser rendering may be omitted when layout, responsive behavior, JavaScript, and interaction are unchanged and source or generated output is sufficient. Report `Browser validation: not required for this change` when omitting it.
- Level 2 browser checks must cover only the affected viewport, route, and interaction. Do not expand a focused check into unrelated desktop/mobile or route regression.
- Level 3 includes desktop 1440x900, mobile 390x844, relevant breakpoint boundaries, keyboard navigation, console, search, major routes, broken assets, and horizontal overflow.
- If required tooling is unavailable, state exactly what could not be run. Never present source inspection alone as a successful build or browser test.
- Documentation-only changes under `AGENTS.md` or `.agents/skills/` do not require rebuilding the site when no production input changed; validate the skill structure and confirm the restricted file diff instead.

## CI and deployment safety

- `build.yml` is a legacy GitHub Pages workflow template stored at the repository root. GitHub Actions only discovers workflows under `.github/workflows/`, so do not treat this file as active CI or as the production deployment path.
- Do not deploy, change Cloudflare settings, move `build.yml`, create a workflow, or alter production credentials unless the user explicitly requests it.
- Keep build validation separate from deployment authorization. Prefer preview verification before production changes when a Cloudflare preview is available.

## Completion report

- Keep the report concise: summarize the requested change, files changed, validation level, commands and versions, browser checks when applicable, and material remaining risks. Do not repeatedly restate known baseline issues unless they affected the result.
- Do not claim that Cloudflare production was validated or deployed unless that action was explicitly performed and its result was observed.
