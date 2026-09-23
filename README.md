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

パイプラインの内部工程は [`config/pipeline.yml`](config/pipeline.yml)、リポジトリ間の受け渡し契約は [`r2r.yaml`](r2r.yaml)、プロジェクト単位のデータ型と型変換の仕事は [`project.yaml`](project.yaml)、批評テキストの編集規約は [`prompts/critique-script.md`](prompts/critique-script.md) にあります。

## 4層オントロジー

似て見える4つを、対象の違いで分離します。正式な語彙とインスタンスは [`ontology.yaml`](ontology.yaml)、RDFとしての表現は [`ontology.ttl`](ontology.ttl) にあります。

| 概念 | 問い | 管理するもの | 管理しないもの |
|---|---|---|---|
| **Topology** | 何が存在し、何と接続できるか | node、port、capability、依存関係、許可されたedge | 実行順、実行状態、編集承認 |
| **Workflow** | いつ、どの順序・条件で実行するか | stage、transition、trigger、retry、runtime state、gate | repo所有権、事業意図、canonical schema |
| **R2R** | repo間で何をどの契約で渡すか | producer、consumer、artifact、provenance、互換性、境界 | 内部の手順順序、批評の論旨、全体トポロジー |
| **AW** | なぜ作るか、何を達成し、どう分解するか | intent、outcome、user story、acceptance criteria、task、decision | runtime実行、データ保存、音声生成の詳細 |

要約すると、**Topologyは地図、Workflowは実行列、R2Rはrepo間の契約、AWは意図から実装への設計図**です。Workflowがrepo境界を越えるときは必ずR2R契約を参照し、AWは承認済みのWorkflowと受入条件へ落とします。

### Project = type transformation work

プロジェクトは単なるrepo名ではなく、**入力データ型を出力データ型へ変換する仕事の所有単位**です。たとえば `idol-db` は `public.source-record.v1` を `canonical.idol-record-set.v1` に正規化し、`idol-research` はそれを `research.analysis-packet.v1` に分析し、`idol-podcast` は批評台本へ言語化します。型、変換、担当プロジェクト、検証条件を [`project.yaml`](project.yaml) で一緒に管理します。

### Schemaとの違い

**Schemaは「ある時点のデータがどんな形か」を定義するもの**です。一方、`project.yaml` はそのschemaを入力・出力として並べ、**時間の流れの中で、誰が、どんな仕事で、どの型へ変換し、何を検証するか**を定義します。

```text
Schema:        A型の形 ──────────────────────────────┐
Project flow:  A型 ──[normalize]──> B型 ──[analyze]──> C型 ──[write]──> D型
                         idol-db          idol-research       idol-podcast
```

したがって、schemaを各型の静的な契約、`project.yaml` を型変換の仕事と時間軸を持つ動的な契約として併用します。`r2r.yaml` はこの流れのうちrepo境界をまたぐ受け渡しだけを定義し、`config/pipeline.yml` は実行順とgateを定義します。

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
