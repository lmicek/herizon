# Deployment

The Herizon website is a plain HTML and CSS site that lives in the `src/` folder. It is
hosted on **GitHub Pages** and deployed automatically by a GitHub Actions workflow whenever
changes are pushed to the `main` branch.

## How it works

```text
git push to main
      |
      v
GitHub Actions runs .github/workflows/static.yml
      |
      v
The ./src folder is uploaded as a Pages artifact
      |
      v
GitHub Pages publishes it at https://ontheherizon.org
```

There is no build step. Whatever is in `src/` is exactly what gets published.

| What | Where |
|------|-------|
| Site files | `src/index.html`, `src/styles.css`, `src/images/` |
| Deployment workflow | `.github/workflows/static.yml` |
| Hosting | GitHub Pages (repository **Settings > Pages**, source set to **GitHub Actions**) |
| Domain and DNS | `ontheherizon.org`, managed in AWS Route 53 |

## Deploying a change

1. Edit the files in `src/`.
2. Open `src/index.html` in a browser to check your changes locally.
3. Commit and push to `main`:

    ```powershell
    git add -A
    git commit -m "Describe your change"
    git push
    ```

4. Open the repository's **Actions** tab on GitHub and watch the
   **Deploy static content to Pages** run. It usually finishes in a minute or two.
5. Visit the live site to confirm the change. You may need to hard refresh
   (`Ctrl+Shift+R`) to bypass your browser cache.

You can also redeploy without a code change: go to **Actions**, choose
**Deploy static content to Pages**, and click **Run workflow**.

## The workflow file: `.github/workflows/static.yml`

This section explains the file one block at a time.

### Name and triggers

```yaml
name: Deploy static content to Pages

on:
  push:
    branches: [main]
  pull_request:
    types: [closed]
    branches: [main]
  workflow_dispatch:
```

- `name` is the label shown in the **Actions** tab.
- `on` lists the events that start the workflow:
    - `push` to `main`: the normal case. Every push deploys the site.
    - `pull_request` with type `closed` against `main`: runs when a pull request is closed.
      For a solo repository this rarely matters, because merging a pull request also pushes
      to `main`, which already triggers a deploy. The `github-pages` environment normally only
      accepts deployments from `main`, so runs started by this trigger on other branches may
      fail or be redundant. You can safely remove this trigger if you want a simpler workflow.
    - `workflow_dispatch`: adds the **Run workflow** button for manual deploys.

### Permissions

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

These limit what the automatic `GITHUB_TOKEN` can do during the run:

- `contents: read` lets the workflow check out the repository code.
- `pages: write` lets it publish to GitHub Pages.
- `id-token: write` lets it prove its identity to the Pages service. The deploy step
  requires this.

### Concurrency

```yaml
concurrency:
  group: "pages"
  cancel-in-progress: false
```

Only one Pages deployment can run at a time. If a second push arrives while a deploy is
running, it waits instead of cancelling the first, so a deploy is never cut off halfway.

### The deploy job

```yaml
jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
```

- `environment: github-pages` ties the job to the repository's Pages environment. This is
  also what shows deployment history and the live URL on the repository page.
- `url` takes the published address from the final step (the step with `id: deployment`).
- `runs-on: ubuntu-latest` runs the job on a GitHub-hosted Linux machine.

### Steps

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4

  - name: Setup Pages
    uses: actions/configure-pages@v5

  - name: Upload artifact
    uses: actions/upload-pages-artifact@v3
    with:
      path: './src'

  - name: Deploy to GitHub Pages
    id: deployment
    uses: actions/deploy-pages@v4
```

1. **Checkout** downloads the repository onto the runner.
2. **Setup Pages** gets the Pages configuration ready.
3. **Upload artifact** packages a folder for publishing. The `path: './src'` line is the
   important one: it makes `src/` the root of the website. Files outside `src/` (the
   `docs/` folder, `README.md`, `pyproject.toml` and so on) are **not** published.
4. **Deploy to GitHub Pages** publishes the uploaded artifact. Its `id: deployment` lets
   the job read the live URL from it.

> **Note:** Because only `src/` is published, these documentation files are not part of the
> public website unless you later add a step that builds and publishes them.

## Troubleshooting

**The workflow didn't run after a push.** Confirm you pushed to `main` and check the
**Actions** tab. Make sure Actions are enabled for the repository.

**The run failed at "Deploy to GitHub Pages".** Check that **Settings > Pages > Source** is
set to **GitHub Actions**.

**The live site shows the README or the wrong page.** Another Pages workflow may be
deploying from the repository root. Only `static.yml` should deploy to Pages, so remove any
other workflow that publishes to Pages (for example the Jekyll sample workflow).

**The site is live but my change isn't showing.** Hard refresh the page, and confirm the
latest run in the **Actions** tab finished successfully.

**Browser says the connection isn't secure.** After changing the custom domain it can take
a while for GitHub to issue a certificate. Wait, then enable **Enforce HTTPS** in
**Settings > Pages**.
