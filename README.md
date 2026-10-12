# dot-helper

## IntelliJ plugin update site (GitHub Pages PoC)

This repo hosts a proof-of-concept GitHub Pages site that serves an
IntelliJ "custom plugin repository": an `updatePlugins.xml` index plus
the plugin ZIP(s) it points at, over HTTPS. The site's content lives in
`site/`, committed to this repo. The workflow that publishes it is
`.github/workflows/pages-update-site.yml`.

This repo is a content bucket: it doesn't build or sign the plugin, it
just deploys whatever is committed under `site/`. The content there
today is a placeholder - a dummy `plugin.xml` zipped up and indexed -
not a signed plugin release. Real plugin signing and a custom domain are
out of scope for this PoC.

### How it's published

On every push to `master` that touches `site/**` or the workflow file
itself (or on a manual `workflow_dispatch` run), the workflow publishes
the committed `site/` directory with GitHub's artifact-based Pages
deploy flow (`actions/configure-pages` -> `actions/upload-pages-artifact`
-> `actions/deploy-pages`). There is no publishing branch in this model -
GitHub stores the deployment itself, built straight from the workflow
run's artifact.

### One-time setup (repo admin)

- The repository must be **public** - GitHub Pages' free tier does not
  serve Pages sites out of private repos.
- In **Settings -> Pages**, set **Source** to **GitHub Actions** (not a
  branch), so `actions/deploy-pages` is allowed to publish.
- **CI-only publishing:** because this deploy model has no publishing
  branch, there is no branch to apply classic branch protection to. The
  equivalent control is on the `github-pages` deployment environment: in
  **Settings -> Environments -> github-pages**, restrict which branches
  may deploy to it to `master` (and add required reviewers if stronger
  control is wanted). That keeps publishing CI-only without a branch to
  protect.

### Limits to plan around

- Site size: 1 GB cap per published site.
- Bandwidth: a soft cap of 100 GB/month, after which GitHub may throttle
  or disable the Pages site.

These are platform limits of GitHub Pages itself, not specific to this
workflow.
