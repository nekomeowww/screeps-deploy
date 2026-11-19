# screeps-deploy

Deployment pipeline for the [screeps](https://github.com/nekomeowww/screeps) monorepo.

## How it works

1. `screeps` repo: `main` is the development branch, `release` is the live strategy.
2. On every push to `release`, the `deploy-release` workflow in `screeps` fires a `repository_dispatch` event (`deploy`) at this repo with the commit SHA.
3. The `deploy` workflow here checks out `screeps` at that exact SHA, builds the bots, and uploads them to Screeps via [`screeps-api`](https://github.com/screepers/node-screeps-api).

Every deploy is pinned to an immutable commit of `screeps`, so a deploy is fully reproducible — and there is no submodule bookkeeping.

## Secrets and variables

Set these in **Settings → Secrets and variables → Actions** of this repo:

| Name | Kind | Purpose |
| --- | --- | --- |
| `PAT_TOKEN` | secret | Token with `contents: read` on `screeps` (to check it out) and permission to receive `repository_dispatch` events. A fine-grained PAT scoped to both repos, or a classic `repo` token, works. |
| `SCREEPS_API_TOKEN` | secret | Screeps API auth token. Create one at <https://screeps.com/a/#!/account/auth-tokens> (or your private server's equivalent). |
| `SCREEPS_HOST` | variable (optional) | Server hostname. Defaults to `screeps.com`. Set to your private server host (for example `screepspl.us`) for private servers. |
| `SCREEPS_BRANCH` | variable (optional) | Screeps code branch to upload to. Defaults to `default`. |

`PAT_TOKEN` must also be set as a secret in the **screeps** repo so its `deploy-release` workflow can dispatch to this repo.

## Manual deploy

Run the `deploy` workflow manually from the Actions tab. It accepts an optional `ref` input (branch, tag, or SHA); leave it empty to deploy the `release` branch head.

## Notes

- `screeps-api upload` replaces the modules it is given. If a bot emits multiple files, pass them all in the upload step or split bots into separate steps.
- To deploy a different bot, add a build + upload step pair in `deploy.yml` for that workspace.
