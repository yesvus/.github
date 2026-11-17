# Workers-first frontend CI contract

See [ops #631](https://github.com/yesvus/ops/issues/631) and [this repository's issue #2](https://github.com/yesvus/.github/issues/2).

This initial reusable workflow deliberately **checks only**. It has no secret inputs, write permission, preview deployment or production deploy step. The first consumer should be [dashboard-template](https://github.com/yesvus/dashboard-template/issues/2).

## Call from a frontend repository

Add an opt-in job in the consuming repository:

```yaml
jobs:
  workers-checks:
    # For public template consumers, review and pin a trusted commit SHA
    # rather than permanently following a moving branch.
    uses: yesvus/.github/.github/workflows/frontend-workers-checks.yml@<reviewed-40-character-commit-sha>
    permissions:
      contents: read
    with:
      node-version: '24'
      pnpm-version: '10'
      run-format: true
      run-browser-tests: true
      check-cloudflare: false # change to true ONLY after cf + Vite plugin are pinned
```

If using a repo with no `format:check`, `test:browser:install`, or `test` scripts, leave the corresponding flags false. `run-browser-tests: true` needs **both** `test:browser:install` and `test`. This v1 contract assumes a pnpm project at the repository root, with a committed lockfile and `lint` / `build` scripts.

## Enable Cloudflare preflight

Only set `check-cloudflare: true` when the consumer:
- has reviewed and committed `cloudflare.config.ts`,
- has pinned `cf` in both package.json and the lockfile, and
- uses a supported build tool, such as the configured Cloudflare Vite plugin for a Vite app.

The workflow runs `cf deploy --dry-run` **without any Cloudflare credential**. This validates its build output and does not upload anything. The normal package `build` script still runs first, preserving extra type checks that `cf build` might otherwise bypass. For a Next.js/vinext consumer, establish its own compatibility checks and only enable this option when supported.

## Preview and deployment are separate follow-ups

- PRs, including fork PRs, get only unprivileged checks. Do **not** add Cloudflare API tokens as a secret to this workflow.
- A separate workflow should deploy isolated previews only for explicitly trusted branches, with scoped identity and no production backend data. Account for preview deletion on close.
- Production release requires an approved GitHub Environment and a smoke test/rollback process. Do not build an ad hoc controller; prefer Cloudflare-native previews and releases.
- Credentials are entered through an owner-approved secure path. Follow [ops #629](https://github.com/yesvus/ops/issues/629) for central secret management, but use Cloudflare Workers **runtime secrets** as the execution target. No plaintext in `VITE_*`, repository files, Terraform state, CI logs or PRs.

## Status

The workflow is a safe foundation, **not a completed deployment pipeline**. Third-party Actions are already pinned to reviewed commit SHAs, and [dashboard-template PR #3](https://github.com/yesvus/dashboard-template/pull/3) exercises the workflow. Follow-up tracked in [#2](https://github.com/yesvus/.github/issues/2): trusted preview lifecycle, secret rotation, guarded production promotion and rollback.

Cloudflare reference: https://developers.cloudflare.com/cf/ci/
