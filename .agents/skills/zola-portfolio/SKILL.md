---
name: zola-portfolio
description: Maintain and review the nokotaro/zola-portfolio Zola static site. Use for changes to its TOML configuration, Tera templates, Markdown content, JSON data, SCSS/CSS, browser JavaScript, Netlify CMS files, search/feed behavior, responsive UI, accessibility, validation, or Cloudflare/GitHub deployment configuration. Do not use as a general Rust application skill.
---

# Zola Portfolio

Work within the user's requested scope and preserve the site's current behavior unless a change is explicitly requested.

## Workflow

1. Inspect `git status`, the requested files, and their consumers before editing.
2. Classify the work and read only the matching references below.
3. Confirm the local Zola version and the Cloudflare production pin when available. While the production pin is unknown, treat 0.18.0 as the verified compatibility baseline. Do not substitute another version for baseline validation; if 0.18.0 is unavailable, continue safe source work and report that baseline validation was not performed instead of migrating configuration without a request.
4. Make the smallest coherent change. Preserve unrelated content, layout, dependencies, and deployment settings.
5. Run the validation appropriate to every affected layer. A successful Zola build is necessary but not sufficient for user-visible changes.
6. Report changed files, commands and versions used, visual/interaction coverage, and anything that could not be verified.

## Reference routing

- Read [references/architecture.md](references/architecture.md) when locating ownership, tracing rendering, or changing cross-cutting behavior.
- Read [references/zola-tera.md](references/zola-tera.md) before changing `config.toml`, templates, macros, partials, search/feed generation, or Zola compatibility.
- Read [references/content-model.md](references/content-model.md) before changing Markdown, front matter, JSON data, CMS fields, or personal information.
- Read [references/styling-accessibility.md](references/styling-accessibility.md) before changing HTML, SCSS/CSS, JavaScript-driven layout, responsive behavior, or accessibility.
- Read [references/validation.md](references/validation.md) for the check matrix and release criteria.
- Read [references/ci-deployment.md](references/ci-deployment.md) only for CI, Cloudflare Pages, GitHub Actions, or deployment work.

## Non-negotiable boundaries

- This is a static site built with Zola; the repository does not contain a Rust application.
- Do not use Jinja2-only syntax without confirming Tera support.
- Do not change a JSON or front-matter schema without checking its template and CMS consumers.
- Do not infer personal facts or silently revise content wording.
- Do not add dependencies, migrate Zola, deploy, or change production configuration as a side effect of another task.
- Do not claim success without naming the validation actually performed.
