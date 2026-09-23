# Work Systemパッケージ抽出計画

## 結論

`idol-podcast` の `work-system/` は、すぐに別リポジトリへコピーするのではなく、**汎用コア、ドメインアダプター、プロジェクト実装**の三つへ分解してから抽出する。最初の候補リポジトリ名は `bonsai/work-system` とする。

抽出の単位は、Workflowの実行順だけではない。Agent、Skill、Tool binding、Task contract、Gate、Provenance、Failure policyを一つの互換性ある契約として扱う。Spotify、`idol-db`、`idol-playlist`、批評プロンプトなどの固有要素は、汎用パッケージから除外してアダプターへ移す。

## 現在地

現在の実践型は [`idol-podcast`](https://github.com/bonsai/idol-podcast) である。そこでは [`work-system/work-system.yaml`](./work-system.yaml) がWork Systemの入口となり、[`config/pipeline.yml`](../config/pipeline.yml) が実行段階を定義し、`.github/agents/` がagentの責務を定義している。型と変換はrootの [`project.yaml`](../project.yaml) に残っているため、現段階では移行用のcompositionとして扱う。

`idol-podcast` は内容層も含む実プロジェクトである。したがって、抽出先へ移す前に、内容の意味を持つ定義と、内容を処理する仕事の定義を分ける必要がある。

## 抽出後の構成

```text
bonsai/work-system                         汎用コア
  core/agent-contract.yaml
  core/skill-contract.yaml
  core/tool-binding.yaml
  core/task-contract.yaml
  core/workflow-contract.yaml
  core/gate-contract.yaml
  core/provenance-contract.yaml
  core/failure-policy.yaml
  schemas/
  docs/

idol-podcast/work-system-adapter.yaml      idol固有の接続
  agents: metadata / song analysis / producer
  tools: Spotify / idol-db / idol-playlist
  content_types: episode / song / script
  workflow_bindings: podcast pipeline

idol-podcast/content/                       内容層
  content.yaml
  research/raw/
  podcast/talkscript/
  prompts/
```

この構成では、`bonsai/work-system` は「何を語るか」を知らない。入力と出力を型付きartifactとして扱い、どのagentがどのskillとtoolで仕事をし、どのgateを通過するかだけを定義する。

## 汎用コアへ移すもの

### Agent contract

Agentは、入力契約、出力契約、責務、禁止事項、使用可能なSkill、必要なGateを宣言する。`metadata-collection-agent` のような名前はドメイン側に残し、コアでは `collector`、`analyzer`、`planner`、`editorial-reviewer` のような役割契約として表現する。

### Skill contract

Skillは、再利用可能な仕事の方法である。Skillには目的、入力、生成物、前提、検証条件、失敗時の扱いを持たせる。現在の `metadata-extraction`、`provenance-capture`、`uncertainty-labeling`、`workflow-decomposition` はコア候補である。ただし、`song-analysis` のように内容領域を直接含むSkillは、汎用の `evidence-bounded-analysis` とドメインSkillへ分ける。

### Tool binding

Toolは実行能力であり、内容の判断そのものではない。コアは、toolの名前、入力形式、出力形式、認証要件、再実行性、side effect、失敗コードを契約化する。SpotifyやGitHubのような具体的なサービス名はコアに埋め込まず、ドメイン側のadapterがtool capabilityへ束ねる。

### Task contract

Taskは、`id`、`skill`、`tools`、`inputs`、`outputs`、`owner`、`done_when`を必須とする。Taskは内容を持たず、型付きartifactを受け渡す。これにより、Podcast、調査、Webサイト、データパイプラインで同じTask形式を使用できる。

### Workflow contract

Workflowは、Taskの順序、依存関係、状態、再試行、停止条件、Gateを管理する。Workflowはデータの意味を解釈せず、入力型と出力型の互換性を確認する。外部repoをまたぐ場合はR2R契約を参照する。

### Gate and provenance

Gateは、次の段階へ進める条件である。最初の汎用Gateは、入力完全性、出典存在、型検証、secret redaction、human review、公開承認とする。Provenanceは、source、source_url、accessed_at、retrieved_at、license、transformation_historyを共通フィールドとして扱う。

## ドメイン側へ残すもの

`idol-podcast` に残すのは、エピソードの主題、アーティストと楽曲、Podcastの編集方針、Spotifyの取得方式、`idol-db`と`idol-playlist`の接続、批評用の禁止事項、台本形式である。これらはWork Systemの例ではなく、Podcastという内容を成立させるための知識である。

`content/content.yaml`、`podcast/`、`research/`、`prompts/`は内容層に残す。`metadata.track-metadata.v1`や`analysis.song-analysis.v1`という型名も、汎用コアの型ではなく、idol-podcast adapterが定義するドメイン型として扱う。コア側では、これらを任意のversioned artifactとして受け取る。

## 移行手順

### Phase 0: 実践型を固定する

現在の`idol-podcast`で、第1回のmetadata収集、楽曲分析、Research、Producer、台本生成を一度完了させる。各段階の入力と出力を記録し、Workflowの実際の失敗点を確認する。この段階では抽象化を急がず、現在の型と人間レビューを正本とする。

### Phase 1: 重複と固有語彙を分類する

`work-system/work-system.yaml`、`config/pipeline.yml`、`.github/agents/`、`project.yaml`、`ontology.yaml`を、コア候補、adapter候補、content候補に分類する。判断基準は「別ドメインでも意味が変わらないか」である。変わる場合はコアへ入れない。

### Phase 2: 契約を先に抽出する

新リポジトリには、まずJSON SchemaまたはYAML契約を置く。最初に実装するのはTask、Artifact、Agent、ToolBinding、Workflow、Gateである。実行エンジンは後回しにし、`idol-podcast`の現在のWorkflowをこの契約で表現できることを検証する。

### Phase 3: idol-podcast adapterを作る

`idol-podcast`側にadapter定義を置き、Spotify、GitHub、`idol-db`、`idol-playlist`、批評プロンプトを汎用capabilityへ接続する。adapterは、ドメイン型とgeneric artifactの間を変換し、source URLと取得時刻を失わないようにする。

### Phase 4: 並行実行する

旧`config/pipeline.yml`と新Work Systemパッケージを同じ第1回入力で実行し、生成されるmetadata、analysis、citation、review statusを比較する。差分がないことを確認するまで、旧定義を削除しない。

### Phase 5: 別リポジトリへ切り出す

契約、adapter例、テストfixture、移行ガイドが揃った時点で、`bonsai/work-system`を作成する。最初のリリースは実行エンジンを含まない契約パッケージとし、Workflow runnerは別の実装として接続する。これにより、汎用化の初期段階で特定ツールやホスティング環境へ固定されることを避ける。

## 完了条件

抽出は、次の条件をすべて満たしたときに完了とする。

| 条件 | 検証方法 |
|---|---|
| 汎用コアにidol固有語彙がない | `spotify`、`idol-db`、`idol-playlist`、`podcast`をコア契約から検索する |
| 既存WorkflowをTask契約で表現できる | 第1回の全stageを変換し、入力・出力・done_whenを確認する |
| ContentとWork Systemが別々に読める | ContentなしでもWork Systemの契約文書を理解できることをレビューする |
| 出典と取得時刻が保持される | metadata fixtureとanalysis fixtureを比較する |
| secretが成果物に残らない | redactionテストを通す |
| 失敗時に再実行できる | stage単位のidempotencyとretry policyを確認する |
| 人間レビューが汎用Gateとして残る | approved／rejected／needs-more-evidenceの状態をfixtureで検証する |
| idol-podcastの出力が変わらない | 旧Workflowと新adapterの成果物ハッシュ・型・引用を比較する |

## 先に作るファイル

抽出先の初期コミットでは、次のファイルだけを作る。

```text
README.md
LICENSE
core/agent-contract.yaml
core/artifact-contract.yaml
core/skill-contract.yaml
core/tool-binding.yaml
core/task-contract.yaml
core/workflow-contract.yaml
core/gate-contract.yaml
core/provenance-contract.yaml
schemas/*.json
examples/minimal-workflow.yaml
examples/human-review.yaml
tests/fixtures/
MIGRATION.md
```

実行エンジン、外部サービス用credential、Podcast固有のprompt、idolデータは初期パッケージに含めない。

## リスクと判断

最大のリスクは、`idol-podcast`の実装をそのまま汎用化して、Podcast固有の意味をWork Systemに埋め込むことである。対策として、コアには「artifactの意味を解釈しない」という境界を置く。

次のリスクは、Workflowの抽象化を先に進めて実運用の失敗を隠すことである。対策として、第1回のmetadata収集と楽曲分析を実践型の検証ケースとして固定し、旧実装と新adapterの並行比較を行う。

最後に、完全な汎用化を一度で達成しようとしない。第一段階の成功条件は、**同じTask契約とGate契約を、内容だけ差し替えて再利用できること**である。複数ドメインで実証できた後に、ontologyやWorkflow runnerをさらに抽象化する。

## References

[1]: https://github.com/bonsai/idol-podcast "idol-podcast project repository"
[2]: https://github.com/bonsai/producer-agent "producer-agent architype repository"
[3]: https://github.com/bonsai/idol-p-agent "idol-p-agent practical type repository"
[4]: https://github.com/bonsai/idol-db "idol-db canonical data repository"
[5]: https://github.com/bonsai/idol-research "idol-research repository"
[6]: https://github.com/bonsai/idol-playlist "idol-playlist repository"
