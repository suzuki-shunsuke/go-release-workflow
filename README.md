# go-release-workflow

GitHub Actions Reusable Workflow for Go Application

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/suzuki-shunsuke/go-release-workflow)
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
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@1cf29d7b17983b901021799b87a55d357cfff790 # v9.0.0
    secrets:
      TAKUMI_GUARD_BOT_ID: ${{secrets.TAKUMI_GUARD_BOT_ID}} # Optional. https://github.com/flatt-security/setup-takumi-guard-golang
    with:
      aqua_version: v2.59.1
      go-version-file: go.mod
    permissions:
      contents: write
      id-token: write
      actions: read
      attestations: write
```

.gitignore

```
third_party_licenses
```

## Requirements

- Cosign
- GoReleaser
- [go-licenses](https://github.com/google/go-licenses)

```sh
aqua g -i sigstore/cosign goreleaser/goreleaser google/go-licenses
```

## LICENSE

[MIT](LICENSE)
