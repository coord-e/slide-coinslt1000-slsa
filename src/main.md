---
marp: true
paginate: true
---

<!-- _paginate: false -->
<!-- _class: title -->

# 5 分で SLSA

2023/9/1 coord_e@coinsLT#1000

---

## サプライチェーン脅威

- 落としてきたパッケージが本当にあなたの望むものであるのか？
- ソフトウェア開発者がソフトウェアを書いてからあなたの手元にパッケージが届くまでの間に、いくつもの Attack surface が存在！

---

![supply chain threats](https://slsa.dev/spec/v1.0/images/supply-chain-threats.svg)
<https://slsa.dev/spec/v1.0/threats-overview>

---

## どうにかならないか。

- sha256sum -c？
- GPG で署名の検証？
- コード署名？

---

## どうにかなっていないよなあ

- 検証できる情報が不十分
  - 結局 F~ 程度しか防げない（ビルドが終わってから～）
- 検証できる情報がものによりばらばら
  - 署名の意味もものによりばらばら
  - 決まったやりかたがない→自動で検証するのがむずかしい

---

## SLSA: Supply-chain Levels for Software Artifacts

- <https://slsa.dev/>
  - Organized within OpenSSF
- セキュリティフレームワーク or レベルがついたベストプラクティス集
- 各 Level に Requirement と Benefit が結びついている

<!-- _footer: 読みサルサらしい -->

---

## 登場人物

- Consumer
  - ソフトウェア成果物を使う
- Producer
  - ソフトウェアを開発する
- Platform
  - リポジトリのホストとか CI プロバイダ的なもの
  - 要は GitHub

---

## 信用関係

- Consumer は Producer を信用していない
  - Producer の制御下の入力はすべて Consumer が検査したい
- Consumer は Platform を信用している
  - どの Platform で生成されたかを認証したいが、それ以外は何も要求しない
- **Producer と Platform の間に明確なセキュリティ境界がある**
- Consumer が信用する部分を Platform まで小さくするためのフレームワーク
  - > Trust platforms, verify artifacts
    > https://slsa.dev/spec/v1.0/principles
  - Level が上がるにつれて Producer への信用が減っていく

---

## SLSA v1.0

- 4 月に v1.0 になった
  <https://slsa.dev/blog/2023/04/slsa-v1-final>
- v1.0 では “Provenance” の生成を中心に Requirement が設定されている
  <https://slsa.dev/spec/v1.0/requirements>

---

## Provenance

<https://slsa.dev/spec/v1.0/terminology#build-model>

- ビルドの出力がどこでどのように生成されたかのメタデータ
- 例「このビルダーにこのパラメータをいれたらこれが出てきた」
  - 「**この Build platform で** Producer が**こうビルドした**結果が**これ**」
- Level 2 以降では Provenance が署名されていることを要求する
- SLSA では推奨される Provenance の形式も定めている

---

## おおむね大丈夫に

<https://slsa.dev/spec/v1.0/threats>

- 検証できる情報が不十分？
  - どうビルドされたかを Provenance として記録
  - Build platform が Provenance に署名
- 検証できる情報がものによりばらばら？
  - Provenance の形式に何らかの標準があればいい
    → **SLSA Provenance** <https://slsa.dev/spec/v1.0/provenance>

<!-- _footer: “Provenance” はビルド来歴メタデータという意味の言葉で、“SLSA Provenance” は in-toto における Predicate の仕様… -->

---

1. 成果物を落とす
1. Provenance を落とす
1. 予期したソースからビルドされたものかどうか検証する
   <https://github.com/slsa-framework/slsa-verifier>

---

## 大丈夫ではない部分

- SLSA では依存関係のレベルは要求しない
  - 普及は容易だが、単に Level N ですと言ってもそんなに効力がない
  - Consumer 側で再帰的に検査するといい
- SLSA v1.0 では Source threats, availability threats はどうにもなっていない
  - 将来のバージョンでどうにかなる予定

---

## In Action(s)

- このスライドをビルドしているリポジトリ
  https://github.com/coord-e/slide-coinslt1000-slsa/
- いろいろなことがあって、GitHub Actions で SLSA Level 3 の Provenance が生成できるようになっている
  - Sigstore とか、Reusable Workflows とか
- 簡単に雰囲気が出るので、みなさんもぜひどうぞ
  https://github.com/slsa-framework/slsa-github-generator
