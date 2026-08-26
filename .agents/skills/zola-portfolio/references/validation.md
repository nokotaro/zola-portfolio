# Validation procedure

Read this reference when planning or signing off a change.

## Preflight

1. Run `git status --short --branch` and preserve unrelated changes.
2. Run `zola --version` and record the result.
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
| JSON data | Parse every edited JSON file; render the consuming homepage section |
| TOML/config | Parse through the target Zola version; inspect every affected `config.extra` consumer |
| CMS YAML | Parse YAML when tooling is available; compare fields with front matter and templates |
| Tera | Exercise every branch affected by flags or optional values, not only the active production branch |
| SCSS/CSS | Inspect compiled `main.css`; test desktop, mobile, and relevant breakpoint boundaries |
| JavaScript | Exercise the interaction, inspect console errors, and check keyboard behavior |
| Search | Open `/search`, enter representative text, and verify result links |
| Tags | Inspect tag list/single pages and feed link generation |
| Feed/SEO | Inspect generated head markup, feed files, sitemap, and robots output |
| Publications/BibTeX | Enable only in an isolated test configuration if necessary; test modal, fetch, copy, and focus behavior |

## Browser sign-off

For HTML, CSS, JavaScript, template, navigation, or design changes:

1. Serve the locally generated site with the validated Zola version.
2. Inspect a representative desktop viewport (1440x900) and mobile viewport (390x844).
3. Inspect breakpoints affected by the change, especially around 936px and 992px.
4. Verify anchor navigation, content reachability, horizontal overflow, missing assets, and console errors.
5. Use keyboard navigation for every affected control.
6. Check accessible names, landmarks, headings, image alternatives, focus visibility, and reduced-motion behavior relevant to the change.

Use a Cloudflare preview only when the user asks for deployment/preview work or the local environment cannot reproduce a provider-specific issue. Never treat a local build as proof of a successful production deployment.

## Instruction-only changes

When only `AGENTS.md` or `.agents/skills/` changes:

- Run the Skill Creator `quick_validate.py` against each changed skill.
- Search for unfinished scaffold markers and sample-only tokens.
- Review links from `SKILL.md` to references.
- Confirm `git diff --name-only` contains only authorized instruction files.
- A Zola rebuild is optional because these paths are not production inputs; a baseline build may still be run to document repository health.

## Release report

State:

- Zola version.
- Exact commands and whether they passed.
- Whether validation used the production pin or the verified compatibility baseline; if neither was available, state that baseline Zola validation was not performed.
- Browser viewports and interactions checked.
- Warnings, skipped checks, and tooling limitations.
- Whether Cloudflare preview or production was touched.
