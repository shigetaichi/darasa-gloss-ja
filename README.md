# darasa-gloss-ja

[darasa-morph](https://github.com/shigetaichi/darasa-morph) 用の日本語訳。
レンマ・語根・型の3種類を持つ。

**ライセンスは GPL v2。** 全文は [`LICENSE`](LICENSE)、理由と著作権の系譜は
[`NOTICE`](NOTICE) にある。解析器本体は MIT だが、こちらは id が calima 由来なので
配布物として分けてある。

## 使い方

darasa-morph の隣に clone すると、既定の探索パスで拾われる。

```sh
git clone https://github.com/shigetaichi/darasa-gloss-ja.git ../darasa-gloss-ja
cd ../darasa-morph
make build-kb      # 検証と充足率
```

別の場所に置いたときは `GLOSS` で指す。

```sh
make build-kb GLOSS="gloss/ja /path/to/darasa-gloss-ja/ja"
```

## 訳を足す

`ja/lemma.tsv` などに1行足して `make build-kb` を回す。

```
كِتاب_1	本	manual	1.0	2026-08-02
```

- **id 昇順**に並べること。検証器が強制する
- `source` は `manual` / `dict` / `mt` のいずれか。**必須**
- id が calima に無ければビルドが止まる（打ち間違いの検出）

## どの語から訳すか

**素の充足率だけを見ると終わらない作業に見える。** 38,568 件あるが、
頻度上位から埋めれば体感カバレッジは先に上がる。

| 訳したレンマ | 頻度加重で届く見込み |
|---|---|
| 100 | 約 32% |
| 1,000 | 約 68% |
| 5,000 | 約 93% |

順番のたたき台は darasa-morph 側で作る。

```sh
cd ../darasa-morph
make handoff                                    # 台帳一式（約1分）
make gloss-worklist WORKLIST_N=5000             # 頻度順・名詞/動詞/形容詞
```

`gloss-worklist.tsv` の列は `rank / lemma_id / diac / bare / pos / gloss_en`。
`lemma_id` と `gloss_en` を見ながら `ja/lemma.tsv` に書き、最後に id 昇順へ並べ直す。

進み具合:

```sh
cd ../darasa-morph && make gloss-check FREQ=lemma-freq.tsv
```

⚠️ **`gloss-worklist.tsv` や `lexicon.tsv` をこのリポジトリにコミットしないこと。**
calima の見出し語一覧そのものになる。**訳を書いた行だけ**を `lemma.tsv` に入れる。

⚠️ **`rank` は CAMeL 頻度リスト（CC BY-SA 4.0）由来である。**
このリポジトリは GPL v2 なので、順位の値そのものを持ち込まない。

## 知っておくと訳しやすいこと

**母音付きの見出し形まで同じレンマが 6,123 件（15.9%）ある。**

```
تَرْجَمَة_1  translation;interpretation
تَرْجَمَة_2  biographies
```

学習者から見て、この2つを区別できるのは**訳語だけ**である
（品詞も同じ noun）。同音語番号が違う id を見かけたら、
**訳し分けが効く形で書くこと。** 詳細は
`darasa-morph/docs/phase4-r2-status.md` §4。

## 対応する calima のバージョン

**calima-msa-r13** 向けである。DB のバージョンを上げると id が変わりうる
ので、そのときは対応表を作って移す作業が要る。
