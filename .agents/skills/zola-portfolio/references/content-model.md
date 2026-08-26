# Content and data model

Read this reference before changing Markdown, JSON, CMS collections, or personal information.

## Markdown sections

| Path | Title/purpose | Current homepage state |
| --- | --- | --- |
| `content/_index.md` | About text and external GitHub cards | Visible |
| `content/blog/_index.md` | Blog section | Visible, no child posts committed |
| `content/projects/_index.md` | Creations/projects | Hidden by `config.extra.show_projects` |
| `content/opensource/_index.md` | Open Source Contributions | Hidden by `show_opensource` |
| `content/publications/_index.md` | Publications | Hidden by `show_publications` |
| `content/search.md` | `/search` route using `search.html` | Generated |
| `content/static/json.md` | `/json` route using `json.html` | Generated as an orphan page |

Do not remove section `_index.md` files merely because their sections are empty or hidden; templates and CMS collections expect those paths.

CMS-created pages in blog/projects/opensource/publications use title, date, description, optional tags, an `extra` object, and Markdown body. Common `extra` keys are `featured`, `link`, and `image`; publications also use `bibtex`.

## JSON-to-template contracts

| Data file | Required shape | Consumer |
| --- | --- | --- |
| `data/achievements.json` | array of `{name, description, badge, proof}` | `templates/partials/about.html` |
| `data/social.json` | array of `{name, icon, link}` | `templates/partials/about.html` |
| `data/skills.json` | groups with `grouping` and `skills`; each skill may be a string or an object with `name`, optional `icon`/`link` | `templates/partials/skills.html` |
| `data/experience.json` | array of `{role, company, summary, range}` | `templates/partials/experience.html` |
| `data/education.json` | array of `{school, degree, major, notes, range}` | `templates/partials/education.html` |

The current skills template does not render a skill object's `link`; do not assume every stored field is visible.

## CMS coupling

`static/admin/config.yml` configures the GitHub backend for `nokotaro/zola-portfolio`, branch `main`, Japanese UI, media under `static/media`, and collections for Blog, Projects, Contributions, Publications, and general settings.

When changing a content schema:

1. Inspect the template consumer.
2. Inspect existing Markdown/JSON values.
3. Inspect the corresponding CMS field definition.
4. Decide how older or missing values render.
5. Validate both hand-authored and CMS-shaped content.

Netlify CMS 2 is loaded from unpkg at `/admin`; Cloudflare hosts the resulting static admin files but does not replace the CMS's configured GitHub authentication model.

## Personal data rule

Names, contact information, employment dates, organizations, education, social URLs, achievements, and biographical wording are not implementation defaults. Change them only from user-supplied facts or an authoritative source the user asked to use. Preserve original wording, language, and date precision unless editing that content is the task.

## Static assets

`static/favicon.ico` and `static/media/avatar.png` are active assets. The committed `.bib` files are inherited publication samples while the publication section is currently hidden. Confirm ownership and usage before deleting or repurposing any static file.
