# AI-Driven-Development-Init-Template

GitHub Copilot cloud agentにやりたいことを伝え、AI設定と開発基盤を整えるためのテンプレートです。
必要なAgentとSkillを同梱しているため、個人PCの設定や追加のインストールは不要です。

## 使い方

1. **テンプレートからリポジトリを作成する**
   **Use this template → Create a new repository** から、新しいリポジトリを作成します。
2. **Cloud agentにやりたいことを伝える**
   [GitHubのAgents画面](https://github.com/copilot/agents)で作成したリポジトリと
   **AI Development Setup** を選び、作りたいものや使いたい技術を入力します。

例えば、次のように依頼します。

```text
Pythonで、JSONファイルの形式をチェックするCLIを作りたいです。
このリポジトリを標準初期化し、最小のCLI本体とテストを作成してください。
必要な規約・品質チェック・基本CIも整え、検証後に変更をcommit・pushしてPRを作成してください。
マージ・デプロイは行わないでください。
```

技術が未定なら「まず構成を相談したい。まだファイルは変更しないで」と伝えることもできます。
作成されたプルリクエスト（PR）の差分と検証結果を確認し、マージは利用者が判断します。

## 何を整えるか

標準初期化では、プロジェクトに合わせて次の不足を補います。

- AI向けの指示と、使用言語・Frameworkの規約
- テスト、lint・format・型検査・ビルドなどの適用できる品質チェック
- 基本CI、PR運用方針、必要な設計記録

既存の構成を尊重し、「AI設定だけ」「CIは変更しない」などの範囲指定にも従います。
アプリ本体は作成を依頼された場合だけ追加し、デプロイ・公開・権限変更は別途承認を求めます。

入口は[AI Development Setup](.github/agents/ai-development-setup.agent.md)、
初期化手順は[aidd-init](.github/skills/aidd-init/SKILL.md)が担当します。
詳細は[Skillの案内](.github/skills/aidd-init/README.md)を参照してください。

## 利用条件

作成先でCopilot cloud agentを利用できるプラン・権限・設定が必要です。
実行にはGitHub Actionsの実行時間とAI creditsを消費します。
[公式の利用条件](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent)と
[クライアント対応](.github/skills/aidd-init/references/client-support.md)を確認してください。
テンプレートの利用からPR作成までの通し実行は未検証です。

配布元では、同梱ファイルをデフォルトブランチへ反映し、
**Settings → Template repository** を有効にしておく必要があります。
