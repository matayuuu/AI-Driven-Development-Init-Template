# Agent向けガイド

## 作業開始時

対象リポジトリと書き込み可能な作業ツリー、依頼範囲、既存差分を確認する。
実装前に[テンプレート初期化方針](.github/template-bootstrap.md)を読み、適用条件を判定する。
未初期化の派生リポジトリでCloud Agentへ実装が依頼された場合は、同梱の
`aidd-init`を使う標準初期化と実装を同じタスクに含める。
この入口があるだけで、派生先の初期化が完了したとは判断しない。
初期化済み、分析のみ、明示的な範囲限定、配布元の保守は方針に従って区別する。

## 構成と読む順序

配布時点ではアプリの言語・Frameworkは未選定で、以下の開発支援設定を同梱する。
派生先では、実装・マニフェスト・利用者の追記を優先して現在の構成を確認する。

| パス | 役割・読む条件 |
| --- | --- |
| [README.md](README.md) | 人向けの用途と利用手順。初期化後は派生先のプロジェクト案内 |
| [.github/copilot-instructions.md](.github/copilot-instructions.md) | 変更時の共通規則 |
| [.github/template-bootstrap.md](.github/template-bootstrap.md) | 初期化の適用判定、README移行、完了条件の正本 |
| [.github/skills/aidd-init/SKILL.md](.github/skills/aidd-init/SKILL.md) | 初期化を実施するときの手順。必要な参照資料だけを読む |
| [.github/agents/ai-development-setup.agent.md](.github/agents/ai-development-setup.agent.md) | 開発基盤だけの分析・整備を明示依頼するときの手動選択用Agent |

初回のアプリ開発は通常のAgentが担当する。専用Agentへの切り替え・自動委譲は不要。
同梱Skillを読めなければ停止し、個人環境の同名Skillで代替しない。

## 検証

配布時点ではアプリ用のbuild/testコマンドはない。推測したコマンドや依存を追加しない。
テンプレート保守では相対リンク、Agent/Skillのfrontmatter、同梱資料・ライセンス、
[評価ケース](.github/skills/aidd-init/evals/evals.json)と
[評価方法](.github/skills/aidd-init/README.md#評価と保守)を確認する。
派生先では実装に対応した検証を実行し、確認済みコマンドと作業場所をREADMEへ記載する。
初期化時にこの構成図と検証案内をプロジェクト固有の内容へ更新し、初期化方針への導線は保持する。
