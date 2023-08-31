# 5 分で SLSA

[coinsLT #1000](https://amakubo.connpass.com/event/288675/) で発表

## Verifying artifacts

成果物（スライド）と Provenance を [Releases](https://github.com/coord-e/slide-coinslt1000-slsa/releases/) からダウンロードしてください。[slsa-verifier](https://github.com/slsa-framework/slsa-verifier/) を使って、成果物が確実にこのリポジトリをソースとしてビルドされたことを検証することができます。

```console
$ slsa-verifier slide.pdf --provenance-path multiple.intoto.build.slsa --source-uri github.com/coord-e/slide-coinslt1000-slsa
Verified build using builder "https://github.com/slsa-framework/slsa-github-generator/.github/workflows/builder_container-based_slsa3.yml@refs/tags/v1.9.0" at commit c888fca24d2bec55e75b025dabd58a7917831b0b
Verifying artifact slide.pdf: PASSED

PASSED: Verified SLSA provenance
```

成果物は [Container-based builder](https://github.com/slsa-framework/slsa-github-generator/blob/main/internal/builders/docker/README.md) を用いてビルダーイメージの上でビルドされています。ビルダーイメージが確実にこのリポジトリをソースとしてビルドされたことを検証することができます。

```console
$ image=$(
    slsa-verifier slide.pdf --provenance-path multiple.intoto.build.slsa \
      --source-uri github.com/coord-e/slide-coinslt1000-slsa \
      --print-provenance \
      | jq -r .predicate.buildDefinition.externalParameters.builderImage.uri
  )
$ slsa-verifier verify-image "$image" --source-uri github.com/coord-e/slide-coinslt1000-slsa
Verified build using builder "https://github.com/slsa-framework/slsa-github-generator/.github/workflows/generator_container_slsa3.yml@refs/tags/v1.9.0" at commit 630dbd37cc808170ea1dc8675e302072fd069903
PASSED: Verified SLSA provenance
```
