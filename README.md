# atina-inc/.github

Shared GitHub Actions building blocks, following the Actions guide (cheap CI).

## Reusable workflows
- `changes.yml` — change detection (`dorny/paths-filter`), 3 min timeout. Output `list`: JSON array of matched filters.

## Standard snippets (cannot be shared via workflow_call, copy into each workflow)
```yaml
# Cancel superseded PR runs; never cancel on main/develop (queue instead).
concurrency:
  group: ci-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: ${{ github.event_name == 'pull_request' }}

on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]
    paths-ignore: ['docs/**', 'aidlc-docs/**', '**/*.md']

jobs:
  x:
    if: github.event.pull_request.draft != true
    timeout-minutes: 10   # every job, 5–15; default is 360
```
Deploy: `needs:` every test job and require `success` (or `skipped` only for path-filtered jobs, with `changes` itself `success`). Its own concurrency with `cancel-in-progress: false`. No nightly `schedule` without a concrete reason.
