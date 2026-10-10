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
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@e51f0489838572acbab6eb550c56a4aac220dd48 # v10.0.1
    secrets:
      TAKUMI_GUARD_BOT_ID: ${{secrets.TAKUMI_GUARD_BOT_ID}} # Optional. https://github.com/flatt-security/setup-takumi-guard-golang
    with:
      aqua_version: v2.64.0
      go-version-file: go.mod
      # go-cache: true # Optional. Enable the cache of actions/setup-go. The default is false
      # packslip: true # Optional. Publish packslip.sigstore.json so mise can install the release. The default is false
      # packslip_bin: pinact # Optional. The executable in each archive. The default is the repository name
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

## Requirements

Cosign and go-licenses are installed by `aqua`, so they are optional. #311

- GoReleaser
- Cosign (optional)
- [go-licenses](https://github.com/google/go-licenses) (optional)

```sh
aqua g -i sigstore/cosign goreleaser/goreleaser google/go-licenses
```
