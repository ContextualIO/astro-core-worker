# @contextualio/astro-core

## 0.3.1

### Patch Changes

- 365a934: fix(sign-in): update `@contextual-io/cds-core` to 0.1.4, so a returning visitor whose session JWT expired is signed straight back in instead of looping between `/sign-in` and the page

## 0.3.0

### Minor Changes

- b4c266d: feat(deployment): build Worker images on release and deploy releases from GitHub Actions

  - A release builds and pushes the images for the site's Workers before it tags the version, signing in to the Contextual organization through its GitHub Connection.
  - The new **Deploy a release** workflow deploys a release to its Workers, reusing the images built for it. Set `CONTEXTUAL_DEPLOY_ON_RELEASE` to `true` to deploy every release automatically.
  - The site build and its release tarball move to a `legacy-build` job, which `CONTEXTUAL_DEPLOYMENT_TYPE=WORKER` skips for a site that runs only as Workers.
  - Building, pushing and deploying run through the public `ContextualIO/deploy-workers-action`, and template syncs through the public `ContextualIO/template-sync-action`, which replaces `bin/template-sync.sh`.
  - Template syncs come from `ContextualIO/astro-core-worker`'s `stable` branch, or the branch in `CONTEXTUAL_TEMPLATE_BRANCH`, and use a token from the organization's GitHub App when `CONTEXTUAL_ORGANIZATION` is set, so they can update workflows; otherwise the `TEMPLATE_SYNC_TOKEN` secret, or the workflow's own token.

## 0.2.3

### Patch Changes

- d136a8a: fix(template-sync): sync from the template's `stable` branch, its released line, instead of `main`

## 0.2.2

### Patch Changes

- 2e8f3fd: docs(template-changelog): name the Worker's managed worker `managed-site-worker`, its name since it was renamed from `site-worker`

## 0.2.1

### Patch Changes

- fb45bd1: chore(deps): upgrade dependencies and record the template sync improvements in the template changelog

  - Upgrade astro 7.3.2 → 7.4.11, @astrojs/node 11.1.5 → 11.1.12, zod 4.6.1 → 4.6.7, @astrojs/check 0.9.10 → 0.9.12, @stytch/vanilla-js ^6.2.0 → ^6.3.0 and @types/node 24.13.4 → 24.19.2, and refresh `yarn.lock`.
  - `TEMPLATE_CHANGELOG.md` describes the upgrades and the template sync improvements for sites.

## 0.2.0

### Minor Changes

- c1dfe6a: feat(deployment): build the site as a Worker with `DEPLOYMENT=WORKER`

  - `DEPLOYMENT` chooses the build: `AGENT` (the default) for a site agent, unchanged, or `WORKER` for a standalone server behind the `site-worker` managed worker's gateway, which reads the visitor's auth config and site user from the gateway's headers.
  - A root `Dockerfile` builds the Worker image. The site serves on the Pod's loopback and exits cleanly when the Pod stops it.

### Patch Changes

- 7715799: fix(template-sync): merge every package.json field and keep pending syncs intact

  - `package.json` merges every field three ways except the site's `name`, `version` and `description`, so template changes to fields such as `resolutions` reach sites.
  - A sync waiting to be merged is updated in place instead of closed and replaced, and is left alone once someone has pushed commits to it.
  - A sync branch whose PR was never opened no longer stops later syncs.
  - Every template changeset the merge brings in is dropped, including ones edited upstream.
  - The sync's own changeset reads `chore(template): sync astro-core @ <commit>`.
  - Sync runs never overlap, and a manually triggered run still syncs and targets the default branch.

## 0.1.0

### Minor Changes

- ea2f380: feat(theme): apply the CDS dark palette, upgrade dependencies, and head the home page with the Contextual logo

  - `<html>` now carries `data-theme` instead of `theme`, the attribute the CDS dark palette is keyed on.
  - Upgrade astro 7.0.3 → 7.3.2, @astrojs/node 11.0.0 → 11.1.5, zod 4.1.12 → 4.6.1 and @stytch/vanilla-js ^6.0.11 → ^6.2.0, plus @astrojs/check, @changesets/cli, @types/node and picomatch.
  - The default home page's heading shows the Contextual logo and links home; the unused Astro logo is removed.
  - Ignore local env files and Claude Code's local settings.

- d34fe10: feat(template-sync): move the template's default file ownership into .gitattributes

  - A site keeps its own copy of any page under `src/pages/`, `src/scaffold/Layout.astro`, `src/styles/theme.css` or `public/favicon.svg` once it has changed it; a site that hasn't keeps following the template.
  - `.templatesyncignore` ships empty and is each site's own list. Its former default entries are now `merge=ours` rules in `.gitattributes`.
  - Sites add their own merge rules below the new marker in `.gitattributes`, where a later line overrides the template's.
