# Repository architecture

Read this reference when locating the owner of behavior or planning a change that crosses multiple folders.

## Runtime model

This repository is the source for a Zola-generated static site. Zola itself is written in Rust, but this repository has no `Cargo.toml`, Rust source files, application server, package manifest, or database. Runtime output is static HTML, CSS, JavaScript, and copied assets.

Production is built and deployed with Cloudflare Workers & Pages and served at `https://about.nokotaro.com/`. No Cloudflare configuration file is committed, so dashboard build settings and any `ZOLA_VERSION` environment variable cannot be inferred from the repository.

## Source map

| Path | Responsibility |
| --- | --- |
| `config.toml` | Production URL, Zola features, Markdown settings, identity fields, and section visibility flags |
| `config.toml.example` | Example site configuration; not the active configuration |
| `theme.toml` | Upstream theme metadata and legacy minimum-version claim |
| `templates/` | Tera layout, page/section templates, macros, and homepage partials |
| `content/` | Markdown section indexes and special generated routes |
| `data/` | JSON data rendered into homepage sections |
| `sass/main.scss` | The only local Sass entrypoint |
| `static/` | Files copied directly into generated output, including CMS and media |
| `build.yml` | Legacy GitHub Pages workflow template; inactive at its current root location |

## Rendering graph

- `templates/base.html` owns the document shell and imports `macros/head.html`, `macros/search.html`, `macros/bibtex.html`, and `macros/fullpage.html`.
- `templates/index.html` extends `base.html` and composes homepage sections from `templates/partials/`.
- `templates/page.html`, `section.html`, `search.html`, `404.html`, taxonomy templates, and publication templates extend `base.html` directly or indirectly.
- `templates/publications/page.html` extends `page.html`; `templates/publications/section.html` extends `section.html`.
- Homepage partials load either a Markdown section with `get_section()` or JSON with `load_data()`.

## Feature ownership

- Homepage visibility and order: `config.toml` plus `templates/index.html` and `templates/macros/fullpage.html`.
- Site identity and avatar: `config.extra` plus `templates/partials/about.html` and `templates/macros/head.html`.
- Skills, experience, education, achievements, social links: corresponding JSON file plus partial.
- Projects, OSS, publications, and blog: section `_index.md`, page front matter, and `templates/partials/section.html`/`templates/section.html`.
- Search: `build_search_index`, Zola-generated Elasticlunr assets, `content/search.md`, and `templates/macros/search.html`.
- Tags/feed: `config.toml`, page taxonomies, and `templates/tags/`.
- BibTeX modal: publication front matter, static `.bib` assets, and `templates/macros/bibtex.html`.

## External browser assets

The document head loads Open Sans, Saira Extra Condensed, Font Awesome 5.15.4, Devicon 2.13.0, Bulma 0.9.3, and jQuery 3.6.0 from CDNs. fullPage.js 3.1.2 and its scroll-overflow vendor script are also loaded from jsDelivr. KaTeX 0.11.0 is conditional and disabled by the active configuration.

Treat these versions and load order as production behavior. An upgrade or self-hosting migration is a separate task requiring browser regression checks.
