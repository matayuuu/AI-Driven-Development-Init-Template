# AI-Driven-Development-Template

GitHub Copilotに、新しいリポジトリのAI設定と開発基盤を整備してもらうためのテンプレートです。
`AI Development Setup` Agentと、その手順を定義する`ai-repo-architect` Skillを同梱しています。
個人PCの設定や、別のSkillのインストールは必要ありません。

想定する使い方は、スマートフォンのブラウザーでテンプレートからリポジトリを作り、
GitHub MobileからCopilot cloud agentへ初期化とプルリクエスト（PR）作成を依頼する流れです。
テンプレートを使うだけでは初期化は実行されません。

## 前提条件

- テンプレートへアクセスでき、新しいリポジトリを作成できること。
- 作成先でCopilot cloud agentを利用できる有料Copilotプラン・権限・設定があること。
  Business / Enterpriseでは管理者のポリシーも確認してください。
- Agent・Skill一式が、作成先のデフォルトブランチに存在すること。
- 初期化する用途・技術・変更を許可する範囲を決めていること。未定なら、まず分析だけを依頼します。

cloud agentはGitHub側の実行環境を使うため、自分のPCを起動しておく必要はありません。
利用にはGitHub Actionsの実行時間とAI creditsを消費します。
契約・ポリシー・利用枠は、作成先ごとに
[cloud agentの利用条件](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent)
を確認してください。このテンプレートで権限や課金設定を変更することはありません。

## テンプレートを公開する側の準備

ファイルの用意とGitHub側のテンプレート設定は別です。

1. この内容をGitHubへ反映し、デフォルトブランチにAgent・Skill・README・LICENSEがあることを確認します。
2. リポジトリの **Settings** で **Template repository** を有効にします。
3. リポジトリのファイル一覧に **Use this template** が表示されることを確認します。

手順の根拠は[テンプレートリポジトリの作成](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-template-repository)です。
このファイル群を置くだけでは、設定の有効化やデフォルトブランチへの反映は行われません。

## スマートフォンから使う

### 1. 新しいリポジトリを作る

スマートフォンのブラウザーで、このテンプレートのGitHubページを開きます。
**Use this template → Create a new repository** から所有者、名前、公開範囲を指定し、
**Create repository from template** を選びます。通常はデフォルトブランチだけを使い、
**Include all branches** は選びません。

作成後、`.github` 配下のAgent・Skillと`LICENSE`が含まれることを確認します。
詳細は[テンプレートからの作成手順](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)を参照してください。
テンプレート由来のリポジトリは独立した履歴を持ち、元テンプレートの更新が自動で同期される構成ではありません。

### 2. GitHub Mobileで初期化を依頼する

GitHub Mobileで **Copilot → New Session** を開き、作成したリポジトリを選びます。
必要ならベースブランチを指定し、Custom Agentの選択欄から **AI Development Setup** を選択します。
用途・技術・承認範囲を含むプロンプトを入力して送信します。

以下はPythonのCLIを新規作成する場合の依頼例です。用途と技術は、自分の要件に合わせて書き換えてください。

```text
このリポジトリに同梱された ai-repo-architect を使用し、標準初期化してください。
用途は、ローカルのJSONファイルを読み、構文が正しければ終了コード0、
不正なら終了コード1を返すCLIです。外部送信は行いません。
技術はPython 3.12の標準ライブラリ、テストはunittestとします。

最小のCLI本体とテストの作成に加え、AI設定、使用言語の規約、
PR運用方針、適用できる品質ゲート、GitHub Actionsの基本CIを整備してください。
既存ファイルとMITの著作権・許諾表示は保持してください。
未決定の重要事項があれば、推測で選ばず一度に一問ずつ確認してください。

変更の検証後、cloud agentの作業ブランチへのcommit・pushとPR作成までを許可します。
マージ、デプロイ、外部サービス作成、権限・公開設定の変更は行わないでください。
実行済みの検証と未確認事項をPRへ記載してください。
```

UIの操作順とCustom Agent選択は
[GitHub Mobileでのcloud agent利用手順](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-mobile)
に基づきます。Mobileで選択欄が見つからない場合は、ブラウザーで
[GitHubのAgents画面](https://github.com/copilot/agents)を開き、対象リポジトリとAgentを選びます。
表示されないAgentを「選択済み」とは扱わないでください。

通常のAgentから依頼する場合は、プロンプトで
`.github/skills/ai-repo-architect/SKILL.md`を読んで従うよう明記できます。
これはSkillを参照する依頼であり、Custom Agentの選択とは異なります。

### 3. PRを確認する

変更内容、既存ファイルの保持、ローカル検証の結果、リモートCIの実行結果を分けて確認します。
cloud agentの作業完了やPR作成は、CI成功・マージ・公開の完了を意味しません。
必要なチェックとレビューを終えた後、マージは利用者が判断します。

## 初期化する範囲

| 依頼 | 作業範囲 |
| --- | --- |
| 分析のみ | 既存構成の確認と提案。ファイルは変更しない |
| 標準初期化 | AI設定、使用言語・テストの規約、PR運用方針、意味のあるテスト、適用できる品質ゲートと基本CI、必要な設計記録 |
| AI設定のみ・成果物限定 | 「AI設定だけ」「AGENTS.mdだけ」「CIは変更しない」など、指定した範囲に限定 |

標準初期化でも既存方式を優先し、不足だけを補います。
アプリ本体は雛形・機能作成も依頼された場合だけ作ります。
言語・Frameworkが未定なら勝手に選ばず、不要なテスト、空のCI、形式だけのADRは追加しません。
Hooks・MCP・デプロイ・公開・権限変更は、別途対象と影響への承認が必要です。

このテンプレート自体はAgentとSkillの配布が目的のため、アプリソース、依存パッケージ、
ビルド、CI、クラウド接続は同梱していません。
初期化後はREADMEをそのプロジェクトの用途と手順へ更新し、引き継いだ部分のライセンス表示は残してください。

## 同梱ファイルと保守

| ファイル | 役割 |
| --- | --- |
| [AI Development Setup](.github/agents/ai-development-setup.agent.md) | 利用者が選ぶ初期化の入口。承認範囲と停止条件を定義 |
| [ai-repo-architect](.github/skills/ai-repo-architect/SKILL.md) | 分析・生成・検証の手順 |
| [Skillの案内](.github/skills/ai-repo-architect/README.md) | 参照資料、評価ケース、配布時の注意 |
| [クライアント対応](.github/skills/ai-repo-architect/references/client-support.md) | cloud agent・CLI等の対応差と公式資料 |
| [LICENSE](LICENSE) | テンプレート全体のMIT License |

Agentは手動選択用で、モデルによる自動起動は無効にしています。
個人環境で使っていた「設定がないリポジトリで初期化を促すInstruction」は同梱していません。
通常の機能依頼を初期化の承認に読み替えず、明示的に依頼してください。
InstructionsやSkillはAIへの作業指示であり、操作の隔離機構や成功保証ではありません。

## 対応状況と未検証の範囲

2026-09-23に確認した公式文書では、リポジトリ内のCustom AgentとSkill、
GitHub Mobileからのcloud agent起動・Custom Agent選択に対応しています。
テンプレートからのリポジトリ作成はGitHub.comの手順を案内しています。
Mobileアプリ単独で新規作成まで完結できるかは未確認です。

実機スマートフォンでの通し操作と、テンプレート由来のリポジトリでのcloud agent実行・PR作成は未検証です。
ファイルの静的検査やローカルでのSkill検出を、クラウドでの実行成功と混同しないでください。

## ライセンスと出典

MIT Licenseです。改変・再配布・商用利用を許可しますが、
**コピーまたは重要な部分には、著作権表示と許諾表示を含める必要があります。**
正式な条件と免責事項は[LICENSE](LICENSE)を参照してください。

matayuuuが管理する`ai-repo-architect`と`AI Development Setup`を基に、
リポジトリ配布・cloud agent向けに参照方法と説明を調整しています。
Skillフォルダー内にも同じMIT Licenseを含めています。
Agentだけを配布する場合も、著作権表示と許諾文を一緒に配布してください。
