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
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@62f819ca556fa57c4a92fcefb393a5fbab4fe73e # v10.0.2
    secrets:
      TAKUMI_GUARD_BOT_ID: ${{secrets.TAKUMI_GUARD_BOT_ID}} # Optional. https://github.com/flatt-security/setup-takumi-guard-golang
    with:
      aqua_version: v2.64.0
      go-version-file: go.mod
    permissions:
      contents: write
      id-token: write
      attestations: write
```

.gitignore

```
third_party_licenses
```

## Requirements

Cosign and go-licenses are installed by `aqua`, so they are optional. #311

- GoReleaser
- Cosign (optional)
- [go-licenses](https://github.com/google/go-licenses) (optional)

```sh
aqua g -i sigstore/cosign goreleaser/goreleaser google/go-licenses
```
