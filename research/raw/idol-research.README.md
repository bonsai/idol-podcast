# idol-research

`bonsai/idol-lab` から分蜂した、アイドル文化の**実証研究・マーケティング・データサイエンス**のリポジトリ。

## 役割分担

- `idol-lab` — 理論・民俗学・概念・オントロジー
- `idol-research` — データ収集・DB・分析・マーケティング・仮説検証

idol-lab に蓄積されたデータ資産のうち、観測・比較・分析に再利用できるものをこちらへ分蜂する。元データの provenance と出典は保持する。

## Research Loop

```text
Observation
  ↓
Research Question
  ↓
Hypothesis
  ↓
Data
  ↓
Analysis
  ↓
Evaluation
  ↓
Insight
  ↓
Next Hypothesis
  ↺
```

## Cases

```text
cases/
└── terasuma/   # 最初の実証ケース
```

Terasuma 固有の研究に閉じず、将来的に複数のアイドル／グループを横断比較する。

## 分蜂元

- `bonsai/idol-lab`
- 分蜂対象: `data/` の観測・イベント・出演者データ、`crawler/` の収集処理など
- 理論本文・エッセイ・概念研究は `idol-lab` に残す

## 原則

- Fact / Inference / Hypothesis を分離
- source / accessed_at / retrieved_at を可能な限り保持
- 数値は出典と観測時点を記録
- マーケティング仮説はデータで検証する
- 一つのケースから一般化せず、比較可能な形で蓄積する
