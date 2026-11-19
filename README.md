# screeps-deploy

Deployment pipeline for the [screeps](https://github.com/nekomeowww/screeps) monorepo.

## How it works

1. `screeps` repo: `main` is the development branch, `release` is the live strategy.
2. On every push to `release`, the `sync-release` workflow in `screeps` updates the `screeps` submodule pointer in this repo and pushes to `main`.
3. On every push to `main`, the `deploy` workflow builds the bots and uploads them to Screeps via [`screeps-api`](https://github.com/screepers/node-screeps-api).

Every deploy is pinned to an immutable commit of `screeps`, so a deploy is fully reproducible.

## Secrets and variables

Set these in **Settings → Secrets and variables → Actions** of this repo:

| Name | Kind | Purpose |
| --- | --- | --- |
| `PAT_TOKEN` | secret | Token with `contents: write` on both repos. Used to check out the private `screeps` submodule and to let `screeps` push submodule updates. |
| `SCREEPS_API_TOKEN` | secret | Screeps API auth token. Create one at <https://screeps.com/a/#!/account/auth-tokens> (or your private server's equivalent). |
| `SCREEPS_HOST` | variable (optional) | Server hostname. Defaults to `screeps.com`. Set to your private server host (for example `screepspl.us`) for private servers. |
| `SCREEPS_BRANCH` | variable (optional) | Screeps code branch to upload to. Defaults to `default`. |

`PAT_TOKEN` must also be set as a secret in the **screeps** repo so its `sync-release` workflow can push to this repo.

## Manual deploy

Run the `deploy` workflow manually from the Actions tab (`workflow_dispatch`). It always deploys whatever commit the submodule currently points to.

## Notes

- `screeps-api upload` replaces the modules it is given. If a bot emits multiple files, pass them all in the upload step or split bots into separate steps.
- To deploy a different bot, add a build + upload step pair in `deploy.yml` for that workspace.
