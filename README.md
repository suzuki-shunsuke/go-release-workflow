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
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@4db55f2aeaca166b2655fa4a80658df1050dc203 # v7.0.0
    with:
      aqua_version: v2.56.1
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
