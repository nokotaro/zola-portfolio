# Zola and Tera conventions

Read this reference before editing Zola configuration or any template.

## Verified version state

Audit date: 2026-08-26.

- Zola 0.18.0 was tested and passes `zola check` and `zola build` for the current source. It is the current verified compatibility baseline.
- Zola 0.19.2, 0.21.0, and 0.23.4 were tested and reject `config.toml` because `generate_feed` is no longer a recognized field.
- No result is implied for untested Zola versions. Upstream documentation must be consulted separately when planning a migration, including syntax-highlighting and Tera-related changes.
- `theme.toml` says `min_version = "0.11.0"`, but that is inherited upstream metadata and is not a production pin.

Until the Cloudflare production `ZOLA_VERSION` is known, validate unchanged site behavior with 0.18.0 when available. If it is unavailable, source inspection and safe editing may continue, but baseline Zola validation must be reported as not performed. Never update deprecated keys merely to accommodate a mismatched local version, and never update only one deprecated key and assume the site is migrated.

## Configuration semantics

The active `config.extra` contract is:

- Identity/assets: `first_name`, `last_name`, `address`, `email`, `favicon`, `avatar`.
- Optional math: `katex`.
- Homepage sections: `show_about`, `show_skills`, `show_projects`, `show_opensource`, `show_publications`, `show_experience`, `show_education`, `show_blog`.

The section flags affect both the homepage section list and fullPage.js menu/anchor arrays. Change both consumers together if the contract itself changes.

## Tera patterns in use

- Inheritance: `{% extends "base.html" %}` with named blocks such as `title`, `rss`, `extra_head`, and `content`.
- Composition: `{% import ... as ... %}` followed by macro calls.
- Data access: `load_data(path="data/...json")` and `get_section(path=".../_index.md")`.
- URL generation: `get_url()` for local assets and anchors; page/section `permalink` for generated content.
- Rendering: `markdown(inline=true)`, `markdown()`, `striptags`, `json_encode`, and `safe`.
- Cross-loop state: `set_global` in `templates/json.html` and `templates/partials/section.html`.
- Tests: `is starting_with(...)`, membership checks such as `"name" in skill`, and page/front-matter conditionals.

Do not translate Jinja2 examples directly. Check Zola's embedded Tera version before using macros, filters, tests, scoping rules, or whitespace controls not already present here.

## Escaping and schema pitfalls

- Values passed through `safe` can render HTML. Trace whether the source is trusted Markdown, repository-owned JSON, or user-managed CMS input before changing the escaping boundary.
- `company`, `summary`, school fields, and section descriptions intentionally pass through Markdown rendering.
- `templates/json.html` must remain valid JSON for any mix of empty, draft, and populated sections. Comma placement and `set_global` state are fragile.
- `page.extra.link`, `page.extra.image`, `page.extra.bibtex`, `page.extra.featured`, and `page.taxonomies.tags` are optional in templates but represented differently in CMS fields. Check missing-value behavior.
- `config.default_language` is not explicitly set, so the current search path falls back to `search_index.en.js`.

## Generated features

- Sass compilation is enabled and emits `main.css`.
- Search-index generation is enabled and emits Elasticlunr assets.
- Tags are configured as a taxonomy.
- The legacy feed setting is enabled, but feed output depends on the compatible Zola version and dated content.
- Zola also generates sitemap and robots output by default for the verified baseline.
