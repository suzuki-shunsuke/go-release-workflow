# go-release-workflow

GitHub Actions Reusable Workflow for Go Application

[workflow](.github/workflows/release.yaml)

## How to use

```yaml
---
name: Release
on:
  push:
    tags: [v*]
permissions: {}
jobs:
  release:
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@e897cae161ab26b8c5471a4749e69432bb4be396 # v10.2.0
    secrets:
      TAKUMI_GUARD_BOT_ID: ${{secrets.TAKUMI_GUARD_BOT_ID}} # Optional. https://github.com/flatt-security/setup-takumi-guard-golang
    with:
      aqua_version: v2.64.0
      go-version-file: go.mod
      # go-cache: true # Optional. Enable the cache of actions/setup-go. The default is false
      # packslip: true # Optional. Publish packslip.sigstore.json so mise can install the release. The default is false
      # packslip_bin: pinact # Optional. The executable in each archive. The default is the repository name
      # split_build: true # Optional. Compile each target in parallel jobs to speed up the build. The default is false
      # split_build_targets: '["linux_amd64", "windows_amd64"]' # Optional. Targets compiled in parallel. The default is linux, darwin, and windows on amd64 and arm64
    permissions:
      contents: write
      id-token: write
      attestations: write
```

.gitignore

```
third_party_licenses
```

## Test the build on refs other than tags

The workflow releases only when it runs on a tag.
On other refs (e.g. `pull_request`), the `build` job runs `goreleaser release --snapshot` to test the build, and the `cosign`, `attestation`, `packslip`, and `release` jobs are skipped.
Signing has side effects such as entries in the transparency log, so the workflow signs only a tag.

Grant the same permissions as on tags.

## Packslip

Set `packslip: true` to publish a signed [packslip](https://packslip.dev/) (`packslip.sigstore.json`) beside the archives, so users can run `mise use packslip:github.com/<owner>/<repo>`.
The `packslip` job runs after the `attestation` job, links the GitHub attestations of the `*.tar.gz` and `*.zip` archives, signs with the workflow's OIDC identity, and passes the bundle to the `release` job.
The job has only `contents: read` and `id-token: write`.

## Split build

GoReleaser builds every target in a single job, so a large application can take several minutes to build.
Set `split_build: true` to compile each target in a parallel `build-cache` job first.
Each job runs `goreleaser build --single-target --snapshot` with the repository's GoReleaser configuration and passes Go's build cache to the `build` job as an artifact.
The `build` job then runs `goreleaser release` as usual, and most of the compilation hits the cache, so only linking remains.

Archives, checksums, SBOMs, and so on are still created by GoReleaser in the `build` job, so the release assets are the same as without `split_build`.
If a `build-cache` job fails, or a target is missing from `split_build_targets`, the `build` job just compiles that target from scratch.

Each target in `split_build_targets` is `<GOOS>_<GOARCH>`.

### When to enable split_build

`split_build` adds a stage of jobs before the `build` job.
Each `build-cache` job takes about 30 seconds to set up, and the `build` job takes a few more seconds to restore the build cache.
So `split_build` pays off only when the build takes much longer than that.

As a guideline, enable it if `building binaries` of GoReleaser takes more than 2 minutes.
You can find the time in the log of the `build` job:

```
  • building binaries
    ...
      • took: 3m37s
```

For example:

| Repository | GoReleaser's build | split_build |
|---|---|---|
| [aquaproj/ar2](https://github.com/aquaproj/ar2) | 3m37s | The build part of the workflow got about 2 minutes faster ([#339](https://github.com/suzuki-shunsuke/go-release-workflow/pull/339#issuecomment-6193863916)) |
| [suzuki-shunsuke/ghtkn](https://github.com/suzuki-shunsuke/ghtkn) | 48s | Not recommended. The overhead of the jobs cancels out the time saved |

## Requirements

Cosign and go-licenses are installed by `aqua`, so they are optional. #311

- GoReleaser
- Cosign (optional)
- [go-licenses](https://github.com/google/go-licenses) (optional)

```sh
aqua g -i sigstore/cosign goreleaser/goreleaser google/go-licenses
```
