# fable-emulation

> 上位モデルの「推論の規律」を、より軽量なモデル上でも近づけるための Claude Skill。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
![Status](https://img.shields.io/badge/status-beta-orange)

上位モデルとの差の多くは、知識量ではなく **規律** に由来する——着手前の分解、失敗しそうな箇所だけの自己検証、確信度の校正、出力密度の管理。このスキルはその4つを Opus / Sonnet 上で再現します。

書いたのは Claude Opus 4.8。`as-is` で公開します。

## なぜ Tier 判定なのか

規律を全タスクに全力適用すると、トークンを食い潰して逆に効率が落ちます。そこで入口に **Tier 判定（T0〜T3）** を置き、難易度とリスクに応じて装備を出し分けます。

| Tier | 対象 | 装備 |
|------|------|------|
| T0 | 事実質問・定義・軽い雑談 | 即答。スキャフォールドなし |
| T1 | 要約・定型コード・単純な文書 | 出力ルールのみ |
| T2 | 複数制約の設計・非自明なデバッグ・分析 | 分解 ＋ 標的検証 |
| T3 | 高リスク／新規性大（契約・金額・本番コード） | フル装備 ＋ 敵対的レビュー |

簡単な問いは素通し、難しい／高リスクな問いだけ分解と検証を起こす。**全力を出さない判断こそが効率を守る**、という設計です。

## 主な機能

- **標的自己検証**：解答全体ではなく「事前に予測した失敗モード」だけを検証。上限は生成の15%
- **双方向ルーティング**：簡単なタスクは Sonnet 降格を提案、高リスクなら Opus 維持／昇格を提案
- **手動オーバーライド**：「軽く」で簡易モード固定、「精度重視で」で全力モード固定
- **オンデマンド可観測性**：「今のTierは？」で内部判定を1〜3行で開示
- **確信度の校正**：推測は推測と明記、検証できなかった点は末尾に正直に書く

## インストール

### claude.ai

1. Settings → Feature Previews で **Code execution** と **Skills** を有効化（Code execution が先。Skills は Pro / Max / Team / Enterprise プラン）
2. Customize → Skills →「+」→ Create skill
3. `fable-emulation/` フォルダを ZIP 化したものをアップロード

### Claude Code

```bash
git clone https://github.com/5umerak/fable-emulation.git
cp -r fable-emulation/fable-emulation ~/.claude/skills/
```

## ライセンス

[MIT](./LICENSE) — 自由に使用・改変・再配布できます。無保証（as-is）。
