# .github

Shared GitHub Actions workflows for the Jenesei Software organization.

## Model

One branch (`main`), one version, one release.

For applications the pipeline does not build. It runs checks, bumps the version in
`package.json`, commits it, tags the commit, and publishes a GitHub Release. The
release carries the changelog and the version number, nothing else. Building an
application is the deployment platform's job: it builds each environment from the tag
with that environment's own variables.

Libraries are different. They publish to a registry, and the registry needs the built
artifact, so the pipeline builds them. Their release is the tag plus changelog, and
the package contents come from the build.

In both cases the release is a named point in history, not a downloadable package.

Version lives in `package.json` and only ever grows. There is no branch suffix and no
build counter, so a version stays valid semver.

## Workflows

| Workflow | Purpose |
| --- | --- |
| `setup-release.yml` | Shared step: runs checks, bumps the version, commits, tags, publishes the GitHub Release |
| `setup-build.yml` | Installs dependencies, runs the build, uploads the artifact |
| `release-app.yml` | Applications: checks and release, no build |
| `release-library.yml` | Libraries: the same, plus a build and `npm publish` to GitHub Packages and/or npmjs.org |
| `setup-readme-versions.yml` | Replaces the `## 🚀 ACTUAL VERSIONS` README block with the latest tag |

Nothing here deploys. The names say `release` because that is all they do.

Storybook is not here. It publishes to GitHub Pages, so each repository keeps its own
`deploy-storybook.yml` alongside its code.

## Usage

Applications call `release-app.yml` from `workflow_dispatch`:

```yaml
jobs:
  release:
    uses: jenesei-software/.github/.github/workflows/release-app.yml@main
    with:
      version_type: patch
      package_manager: npm
    secrets:
      ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Libraries call `release-library.yml` instead and pass `registry_type`. They may also
pass `build_folder` and `build_command`; the build output is what npm publishes.

The caller file can carry the same name as the shared workflow, so an application ends
up with `.github/workflows/release-app.yml` that calls
`jenesei-software/.github/.github/workflows/release-app.yml@main`.

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

`registry_type` is `npm`, `github` or `all`, and defaults to `github`. When `NPM_TOKEN`
is empty the npmjs.org step is skipped with a warning and GitHub Packages still
publishes.

### Outputs

`release-app.yml` and `release-library.yml` expose `version` and `tag` so a caller can
use them in later jobs.

Declare them with the `jobs` context, not `needs`:

```yaml
on:
  workflow_call:
    outputs:
      version:
        value: ${{ jobs.release.outputs.version }}
```

The `workflow_call` outputs block has no access to `needs`. Inside a job, where the
`needs` context exists, `needs.release.outputs.version` is the correct form.

## Permissions

A workflow that declares `permissions:` loses every scope it does not list. Anything
reading from GitHub Packages therefore needs it spelled out:

```yaml
permissions:
  packages: read
  contents: write
```

Without `packages: read`, an install step fails with
`403 Permission installation not allowed to Read organization package`.

## Checks

Every repository exposes the same command:

```json
"check": "biome check src && tsc -p tsconfig.json"
```

Biome runs first because it is cheap, TypeScript second because it is not. Some
repositories check a different path set or use `tsc --noEmit`; the workflow only
cares that the script exists and exits zero.

A repository without a `check` script is skipped with a warning, so opting in is a
single line.

## Environment

Build-time variables belong to the deployment platform, not the repository. Each
environment configures its own values when it builds.

Repositories document the required keys in `.env.template` and gitignore real `.env`
files. The pipeline never needs them.

Applications expose their version to the frontend by reading `package.json` at build
time, not through a pipeline-supplied variable. Vite injects it as `__APP_VERSION__`.

`setup-build.yml` still exports `env_property` during the build, defaulting to
`VITE_APP_VERSION`, but no library reads it: a library's version comes from
`package.json` at install time. It is dead weight left from the era when applications
were built here.

## What this replaces

The `dev` / `test` / `prod` branches and the `build_*` branches they fed are gone.
`build:*` scripts keyed to a branch name and the `add_branch_name` /
`add_build_number` inputs no longer exist.