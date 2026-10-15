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
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@f3116b4a0418dc22a5332182659bbaaa523fcfa1 # v10.1.0
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
It adds a job per target, so this only pays off when the build takes long. For a small application, the overhead of the jobs can exceed the time saved.

## Requirements

Cosign and go-licenses are installed by `aqua`, so they are optional. #311

- GoReleaser
- Cosign (optional)
- [go-licenses](https://github.com/google/go-licenses) (optional)

```sh
aqua g -i sigstore/cosign goreleaser/goreleaser google/go-licenses
```
