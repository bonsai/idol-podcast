# idol-podcast

`idol-db` と `idol-research` の成果から、根拠のあるアイドル批評を選抜・分析・言語化し、最後に `podcast-generator` へ渡すための編集パイプラインです。

> **Public data is the input. Analysis is the product. Critique is the public conversation.**

## パイプライン

```text
idol-db
  canonical public data / crawler / schema / provenance
        ↓ select evidence
idol-research
  observation / comparison / hypothesis / validation
        ↓ analysis packet
idol-podcast
  critique framing / script / show notes / citations
        ↓ human editorial review
podcast-generator
  text-to-speech / multi-speaker audio / transcript
        ↓ manual release
Podcast episode
```

## 役割分担

| 層 | 責務 | 出力 |
|---|---|---|
| `idol-db` | 公開情報の取得・正規化・出典管理 | canonical data、source URL、取得日時 |
| `idol-research` | 観測、比較、仮説、評価、次の検証設計 | analysis packet |
| `idol-podcast` | 選抜論点の批評構成、言語化、出典・注意点の整理 | 台本、概要欄、引用一覧 |
| `podcast-generator` | 承認済みテキストの音声化 | 音声、字幕、生成メタデータ |

## 実行契約

パイプラインの内部工程は [`config/pipeline.yml`](config/pipeline.yml)、リポジトリ間の受け渡し契約は [`r2r.yaml`](r2r.yaml)、批評テキストの編集規約は [`prompts/critique-script.md`](prompts/critique-script.md) にあります。

入力は次のフィールドを持つことを前提にします。

```json
{
  "facts": [],
  "inferences": [],
  "hypotheses": [],
  "counter_hypothesis": "",
  "confidence": "medium",
  "next_validation": "",
  "citations": []
}
```

各事実には、可能な限り `source`、`source_url`、`accessed_at`、`retrieved_at`、`license` を付けます。`idol-db` のraw dataをそのまま商品化するのではなく、`idol-research` の分析と `idol-podcast` の編集によって、文脈・比較・反対仮説を備えた批評として価値化します。

## 音声化の境界

`podcast-generator` は音声を生成するアダプターであり、事実の出典や台本の正しさを判断する層ではありません。したがって、音声生成前に人間の編集レビューを必須とします。台本・出典・生成時刻・音声設定・音声ファイルのハッシュを一緒に保存し、後から検証可能にします。

private情報、個人情報、未確認の噂、根拠のない人物評価は公開用台本へ含めません。実在の個人・グループ・運営を扱う場合は、出典、反対仮説、データの限界、必要に応じた反論機会を記録します。
