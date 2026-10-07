# .github

Shared GitHub Actions workflows for the Jenesei Software organization.

## Model

One branch (`main`), one version, one release.

The pipeline does not build. It runs checks, bumps the version in `package.json`,
commits it, tags the commit, and publishes a GitHub Release. The release carries the
changelog and the version number, nothing else.

Building is the deployment platform's job. A release is a named point in history that
the platform builds from, not a package it downloads.

Version lives in `package.json` and only ever grows. There is no branch suffix and no
build counter, so a version stays valid semver.

## Workflows

| Workflow | Purpose |
| --- | --- |
| `setup-version.yml` | Runs checks, bumps the version, commits, tags, publishes the GitHub Release |
| `deploy-node.yml` | Applications: calls `setup-version.yml` |
| `deploy-library.yml` | Libraries: the same, plus `npm publish` to GitHub Packages and/or npmjs.org |
| `setup-readme-versions.yml` | Replaces the `## 🚀 ACTUAL VERSIONS` README block with the latest tag |

## Usage

Applications call `deploy-node.yml` from `workflow_dispatch`:

```yaml
jobs:
  release:
    uses: jenesei-software/.github/.github/workflows/deploy-node.yml@main
    with:
      version_type: patch
      package_manager: npm
    secrets:
      ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Libraries call `deploy-library.yml` instead and pass `registry_type`.

### Inputs

`version_type` is `major`, `minor`, `patch` or `none`. `none` reuses the version
already in `package.json`, which is how you redo a broken release.

`explicit_version` overrides `version_type` when you need to pin a specific version,
for example the first release after moving off the old scheme.

If the resulting tag already exists, the run fails before pushing anything rather
than creating a duplicate.

`check_command` names the script to run before releasing. Empty skips checks. When
the repository has no such script the workflow warns and continues, so a repository
can opt in by adding a single `check` script.

`changelog_preset` is a `conventional-changelog` preset, for example `angular` or
`conventionalcommits`. Empty disables changelog generation and the release falls back
to GitHub's own generated notes.

## Checks

Every repository exposes the same command:

```json
"check": "biome check --no-unsafe-fixes src && tsc --noEmit"
```

Biome runs first because it is cheap, TypeScript second because it is not. The
`--no-unsafe-fixes` flag guarantees the check never rewrites files.

## Environment

Build-time variables belong to the deployment platform, not the repository. Each
environment configures its own values when it builds.

Repositories document the required keys in `.env.template` and gitignore real `.env`
files. The pipeline never needs them.

## What this replaces

The `dev` / `test` / `prod` branches and the `build_*` branches they fed are gone.
`build:*` scripts keyed to a branch name and the `add_branch_name` /
`add_build_number` inputs no longer exist.