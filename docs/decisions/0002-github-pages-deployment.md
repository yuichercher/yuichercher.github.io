# 0002 · Deploy to GitHub Pages with GitHub Actions

English · [中文](0002-github-pages-deployment.zh.md)

- Status: accepted
- Date: 2026-11-02

## Context

The current Astro 7 sample blog produces static files and needs to be published at `https://yuichercher.github.io/`. Its source is already in the `yuichercher/yuichercher.github.io` repository. Deployment should be automatic after changes reach `main`, with a manual way to rebuild the site.

The repository has a committed npm lockfile, and the engineering conventions require a pinned Node version. This task establishes deployment for the existing sample blog; replacing the template content and implementing the design are separate changes.

## Decision

Use GitHub Pages with a GitHub Actions workflow in `.github/workflows/deploy.yml`.

- Set Astro's `site` to `https://yuichercher.github.io/`. Canonical URLs, the sitemap, and RSS links then use the public address. Keep the default `base` of `/`: a repository named `<username>.github.io` is served at the domain root, not at a repository subpath.
- Keep Astro's default static output. No server adapter is needed because Pages serves the generated files.
- Trigger the workflow on pushes to `main` and through `workflow_dispatch`. A manual run on another branch may build, but the deploy job only publishes `main`.
- Pin Node to `24.16.0` in `.node-version`, matching the local runtime used to verify this change and satisfying Astro's Node requirement. `actions/setup-node` reads this file, so local version managers and CI can share one pin.
- Use separate build and deploy jobs. The build job checks out the source, caches npm downloads, runs `npm ci` and `npm run build`, then uploads `dist/` with `actions/upload-pages-artifact`. `npm ci` installs the locked dependency tree and fails if the manifest and lockfile disagree.
- Run `actions/deploy-pages` only after the build succeeds, publishing the artifact through the `github-pages` environment. Give the build job `contents: read`; grant `pages: write` and `id-token: write` only to the deploy job. GitHub supplies the authentication, so no personal access token or additional repository secret is needed.
- Serialize workflow runs with the `pages` concurrency group and `cancel-in-progress: false`, so a new push does not interrupt a deployment already running.

### Initial activation and later deployments

1. In the repository's **Settings → Pages → Build and deployment**, set **Source** to **GitHub Actions**. This is a repository setting, not something committing the workflow changes automatically.
2. Push the reviewed workflow and configuration to `main`. In the **Actions** tab, inspect the **Deploy to GitHub Pages** run; both jobs must succeed before the new site is published.
3. Open `https://yuichercher.github.io/`. For a later manual deployment, select **Run workflow** and choose `main`.

## Alternatives

### Astro's composite deployment action

The official Astro guide recommends `withastro/action`, which combines installation, building, and artifact upload. It is convenient and requires fewer workflow lines.

Not chosen here: its current v6 implementation runs `npm install`. Explicit steps let us use `npm ci` and read the Node pin directly from `.node-version`, while keeping each stage easy to inspect in the Actions log. The tradeoff is maintaining a few more workflow steps.

### Publish generated files from a branch

Committing `dist/` to `main` or a separate `gh-pages` branch would also let Pages serve the site.

Not chosen: generated files would need a separate commit or branch update on every deployment. Uploading a Pages artifact keeps build output out of source control and avoids giving the workflow permission to push commits.

## Consequences

- After initial activation, each push to `main` publishes the sample blog automatically. A failed build prevents the deploy job from running and leaves the previously published version in place.
- `dist/` stays ignored, and no application dependency is added. Node and the workflow action versions still need deliberate maintenance.
- Local verification uses Node `24.16.0`, `npm ci`, and `npm run build`. A successful local build verifies the output, but the first successful Actions deployment is still needed to verify repository permissions and Pages settings.
- GitHub Pages supports this static build. Future features requiring a server at request time would require revisiting the hosting approach.

## References

- [Astro: deploy to GitHub Pages](https://docs.astro.build/en/guides/deploy/github/)
- [GitHub: custom workflows with GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)
- [Astro deployment action v6 implementation](https://github.com/withastro/action/blob/v6/action.yml)
