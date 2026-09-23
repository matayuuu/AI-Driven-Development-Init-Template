# AI-Driven-Development-Init-Template

GitHub Copilot cloud agentへアプリの実装を依頼するときに、
`aidd-init`によるAI設定・開発基盤の初期化を同じタスクへ含めるためのテンプレートです。
通常のAgentが読む入口とSkillを同梱しているため、専用Agentの選択や個人PCの設定は不要です。
対象は未初期化の派生リポジトリで、初期化済みのプロジェクトを毎回作り直す方針ではありません。

## 使い方

1. **テンプレートからリポジトリを作成する**
   **Use this template → Create a new repository** から、新しいリポジトリを作成します。
2. **Cloud agentにやりたいことを伝える**
   [GitHubのAgents画面](https://github.com/copilot/agents)で作成したリポジトリを選び、
   通常のAgentへ作りたいものや使いたい技術を入力します。`aidd-init`の名前や
   「初期化して」という追記は不要です。
3. **初期化と実装の結果を確認する**
   アプリだけでなく、プロジェクト用README・AI設定・テスト・適用できる品質チェック・基本CIを確認します。
   PRが必要な入口では作成を依頼し、差分と検証結果を確認してから利用者がマージを判断します。

例えば、次のように依頼します。

```text
ブラウザーで動く横スクロールのジャンプアクションゲームを作成してください。
```

このような機能の依頼でも、未初期化なら標準初期化を含める設定です。
技術の重大な未決事項や権限不足があれば、Agentは確認・停止します。
「まず構成を相談したい。まだ変更しないで」「初期化しない」「コードだけ」
「CIは変更しない」などの指定は、既定の初期化より優先します。
ローカルCLI・IDEでは自動初期化せず、初期化の明示依頼・了承に従います。

## 何を整えるか

標準初期化では、プロジェクトに合わせて次の不足を補います。

- AI向けの指示と、使用言語・Frameworkの規約
- テスト、lint・format・型検査・ビルドなどの適用できる品質チェック
- 基本CI、PR運用方針、必要な設計記録
- 派生先のアプリ用READMEと、実装構造・検証入口に合わせたAI設定

既存の構成を尊重し、「AI設定だけ」「CIは変更しない」などの範囲指定にも従います。
アプリ本体は作成を依頼された場合だけ追加し、デプロイ・公開・権限変更は別途承認を求めます。

派生先のREADMEは、テンプレートの説明を末尾へ残すのではなく、実際のアプリの案内へ置き換えます。
利用者の追記とMITの著作権表示・許諾表示は保持します。

## 読み込みと初期化の判定

[AGENTS.md](AGENTS.md)と[共通Instructions](.github/copilot-instructions.md)が、
[初期化方針](.github/template-bootstrap.md)を確認する入口です。
同梱Skillや汎用の入口があるだけでは、プロジェクト固有の初期化済みとは判定しません。
既存アプリが先に追加されている場合も、設定内容を確認し、不足だけを補います。
初期化済みの設定がある場合や、この配布元自体の保守では、自動初期化しません。

初期化手順の正本は[aidd-init](.github/skills/aidd-init/SKILL.md)です。
[AI Development Setup](.github/agents/ai-development-setup.agent.md)は、
開発基盤だけの分析・初期化・改善を明示的に依頼するときの手動選択用として残しています。
機能実装と初期化を同時に行う入口として選ぶ必要はありません。
詳細と評価方法は[Skillの案内](.github/skills/aidd-init/README.md)を参照してください。

既存の派生リポジトリへ、この更新は自動反映されません。
古いテンプレートから作成した場合は、入口・初期化方針・対応するSkillの更新を取り込み、
対象ブランチにそろえたうえで新しいCloud Agentタスクを開始してください。

## 利用条件

作成先でCopilot cloud agentを利用できるプラン・権限・設定が必要です。
実行にはGitHub Actionsの実行時間とAI creditsを消費します。
[公式の利用条件](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent)と
[クライアント対応](.github/skills/aidd-init/references/client-support.md)を確認してください。
InstructionsはAgentへの作業指示であり、Skillを必ず起動するHookや権限の自動承認ではありません。
この初期化経路でのテンプレート作成からCloud AgentのPR作成までの通し実行は未検証です。

配布元では、同梱ファイルをデフォルトブランチへ反映し、
**Settings → Template repository** を有効にしておく必要があります。
