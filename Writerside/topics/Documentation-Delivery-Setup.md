# Documentation Delivery Setup

<link-summary>How this documentation is built, validated, indexed, and published.</link-summary>
<card-summary>Docker + CI/CD + GitHub Pages + Algolia setup for multi-platform delivery.</card-summary>
<web-summary>Internal implementation notes for publishing this WriterSide project reliably.</web-summary>

<primary-label ref="audience-ops"/>
<secondary-label ref="internal"/>

## What is configured

- WriterSide instance: `Writerside/hi`
- Starting page: `Starting-page.topic`
- Redirect file: `Writerside/redirection-rules.xml`
- Labels file: `Writerside/labels.list`
- Build profile features: sitemap, indexability, offline docs, Algolia search settings

## Docker local build

The repository includes cross-platform Docker files so docs can be built and served consistently on macOS, Windows, and Linux.

<tabs>
    <tab title="macOS / Linux">
        <code-block lang="bash">docker compose up --build docs-preview</code-block>
    </tab>
    <tab title="Windows (PowerShell)">
        <code-block lang="powershell">docker compose up --build docs-preview</code-block>
    </tab>
</tabs>

After startup, open `http://localhost:8080` in any supported browser.

## CI/CD and GitHub Pages

The workflow at `.github/workflows/docs.yml` does the following:

1. Builds docs with `JetBrains/writerside-github-action`.
2. Runs Writerside checks.
3. Deploys the static artifact to GitHub Pages.
4. Optionally publishes Algolia indexes when required secrets and variables exist.

<code-block lang="yaml" src="../codeSnippets/ci/docs-workflow-overview.yml"/>

## Algolia setup

Set these in GitHub before enabling index publishing:

- Repository variable: `ALGOLIA_APP_NAME`
- Repository variable: `ALGOLIA_INDEX_NAME`
- Repository variable: `ALGOLIA_SEARCH_ONLY_KEY`
- Repository variable: `CONFIG_JSON_PRODUCT`
- Repository variable: `CONFIG_JSON_VERSION`
- Repository secret: `ALGOLIA_KEY`

<code-block lang="text" src="../codeSnippets/ci/algolia-required-values.txt"/>

## Indexing, sitemap, and offline docs

Configured in `Writerside/cfg/buildprofiles.xml`:

- `<sitemap .../>` enables sitemap generation.
- `<noindex-content>false</noindex-content>` allows search engine indexing.

Configured in `.github/workflows/docs.yml`:

- The built documentation archive is copied to `downloads/offline-docs.zip` during deployment.
- The same archive remains available as a CI artifact for internal distribution.

After GitHub Pages deployment, the package is available at `https://<org-or-user>.github.io/<repo>/downloads/offline-docs.zip`.

## Release checklist

- Validate in Chrome, Edge, Firefox, and Safari.
- Validate on at least one macOS, Windows, and Linux environment.
- Confirm redirect behavior for known legacy links.
- Confirm sitemap is present in generated artifacts.

<seealso>
    <category ref="delivery">
        <a href="https://www.jetbrains.com/help/writerside/deploy-docs-to-github-pages.html" summary="Official deployment guide."/>
        <a href="https://www.jetbrains.com/help/writerside/configure-search.html" summary="Official search and Algolia setup guide."/>
    </category>
</seealso>
