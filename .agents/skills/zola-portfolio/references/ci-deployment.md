# CI and deployment

Read this reference only for CI, GitHub Actions, Cloudflare Pages, or deployment work.

## Production path

The repository owner states that Cloudflare Workers & Pages builds and deploys the site at `https://about.nokotaro.com/`. Cloudflare's normal Zola Pages contract is a build command such as `zola build` with output directory `public`. The exact dashboard settings and `ZOLA_VERSION` environment variable are external state and are not committed here.

Never deploy, change dashboard settings, rotate credentials, or modify the production branch without an explicit request and the required authorization.

## Current `build.yml`

`build.yml` describes a legacy GitHub Pages workflow that:

- Triggers on pushes to `main` and `master` if installed as a workflow.
- Uses `actions/checkout@v2`.
- resolves Zola through the GitHub Releases `latest` endpoint and downloads a matching Linux archive.
- runs only `zola build`.
- deploys `public/` with `peaceiris/actions-gh-pages@v3` on `main`.

The file is at repository root, not `.github/workflows/`, so GitHub Actions does not discover it as an active workflow. Treat it as legacy/reference material, not the current CI or production path.

## Risks in the legacy workflow

- `latest` is not reproducible. During the 2026-08-26 audit it resolved to the tested Zola 0.23.4 release, which rejected the current `generate_feed` setting; future values of `latest` remain unverified.
- Build and deployment are coupled; there is no independent pull-request validation job.
- Action major versions are old and no permissions block is declared.
- The downloaded release is selected dynamically and is not pinned by version or digest in the repository.
- It runs `zola build` but not an explicit `zola check`.
- Supporting both `main` and `master` is unnecessary unless both branches are intentionally maintained.

## Improvement candidates (report first; do not apply incidentally)

1. Record the actual Cloudflare Pages build command, output directory, and `ZOLA_VERSION` in maintained documentation or provider configuration.
2. Temporarily pin the verified legacy Zola version, then plan and test a deliberate migration to a supported current Zola release.
3. Add provider-independent CI for pull requests that runs `zola check` and `zola build` without deployment credentials.
4. Use maintained action versions, least-privilege `permissions`, concurrency cancellation where appropriate, and a reproducible Zola installer/version.
5. Decide whether to remove, archive, or clearly label root `build.yml` after confirming it has no external consumer.
6. Keep Cloudflare preview deployment and production promotion separate from local validation and GitHub CI success.

Any CI modernization that changes Zola version must be treated as a compatibility migration covering TOML keys, Markdown highlighting, feeds, Tera syntax, generated output, search, and browser regression testing.
