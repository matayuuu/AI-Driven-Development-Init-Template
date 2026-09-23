# クライアント対応と一次情報

公式文書の確認日: 2026-09-23。対応する配置と設定の資料であり、
すべての実行環境での動作確認や将来の互換性保証ではない。
初期化時には現在のホスト、OS、CLI・IDEのバージョン、必要な機能の現行仕様を確認する。

## 配布する構成

| 種類 | このテンプレートの配置 | 公式文書上の対応 |
| --- | --- | --- |
| Agent向けの入口 | `AGENTS.md` | cloud agentが参照するAgent instructions |
| 共通Instructions | `.github/copilot-instructions.md` | リポジトリの依頼へ適用する共通指示 |
| 初期化方針 | `.github/template-bootstrap.md` | 上記の入口から明示的に読む文書。独自の自動検出ファイルではない |
| Custom Agent | `.github/agents/ai-development-setup.agent.md` | GitHubのcloud agent、CLI等。ホストごとの設定差を確認する |
| Skill | `.github/skills/aidd-init/SKILL.md` | cloud agent、CLI、Copilot app、VS Code等 |
| Skillの参照資料 | 同じSkillフォルダーの`references` | 必要なときに本文から参照する |
| 評価ケースとライセンス | 同じSkillフォルダーの`evals`と`LICENSE` | 保守・配布用。配置だけで評価は実行されない |

公式資料:
[Custom Agentの作成](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents)、
[Agent Skillsの概要](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)、
[Skillの追加](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills)。

個人PCのホームディレクトリはcloud agentへ自動配布されない。
この配布版は手順と参照資料をリポジトリ内に同梱し、同名の個人Skillや外部フォルダーを前提にしない。
生成する設定も個人の絶対パスや未同梱資料へ依存させない。

## 初回実装での読み込み

[リポジトリInstructionsの公式手順](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions)
では、`.github/copilot-instructions.md`と`AGENTS.md`の対応を説明している。
一方、[Skillの利用](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills#how-copilot-uses-agent-skills)
はプロンプトとdescriptionに基づくモデルの選択であり、配置だけで毎回起動する仕組みではない。

そのためこのテンプレートでは、通常のAgentが共通の入口から初期化方針を読み、
適用条件を満たす場合は同梱SKILL.mdを明示的に読む。Custom Agentの自動起動には依存しない。
InstructionsとSkillの配置・静的な判断評価は、実際のcloud agentでの読み込みと作業完了の保証ではない。
利用するブランチへ入口・方針・Skillが反映されていることを確認し、新しいタスクで評価する。
既存の派生先・進行中のタスクへ、配布元の更新が自動同期されるとは説明しない。

## Custom Agentの設定

[設定リファレンス](https://docs.github.com/en/copilot/reference/custom-agents-configuration)で
確認した、この配布版が使用する項目は次のとおり。

- `name`、`description`、`tools`、`user-invocable`、`disable-model-invocation`を使う。
  `user-invocable: true`で利用者が選択でき、`disable-model-invocation: true`で
  モデルによる自動起動を無効にする。選択だけを編集の承認とは扱わない。
- `target`は省略し、特定ホストだけに限定しない。架空の値や不要な`model`は加えない。
- 本文の上限は30,000文字。`infer`は廃止済みなので新規採用しない。
- `read` / `search` / `edit` / `execute`を使う。`web`はCLI等向けで、
  cloud agentでは適用されない。使えるToolはホストごとに確認する。
- `tools`を省略すると全Toolが対象になるため、明示した最小限の一覧を維持する。
  一覧はToolの公開範囲のフィルターであって、`execute`をテスト専用に隔離するものではない。
  未認識の名前は無視されるため、名前を書いただけで利用可能と判断しない。
- `agent`、MCPのワイルドカード、`mcp-servers`、`handoffs`は同梱Agentで指定しない。
  `handoffs`等のIDE固有の動作を、cloud agent共通の自動実行とみなさない。

公式の作成手順では、Agent定義をcommitし、デフォルトブランチへ反映してから選択欄を更新する。
タスクで使用する定義は対象リポジトリ・ブランチのバージョンに依存するため、
選択したブランチにもSkillと参照資料があることを確認する。
古いセッションが新定義を反映済みとは仮定しない。

## cloud agentの作業と承認

[cloud agentの概要](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent)と
[開始方法](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)
では、GitHub側での作業ブランチ・commit・pushと、入口や依頼に応じたPR作成が説明されている。
PRを自動で作る入口と、作業後に作成を指示する入口があるため、一律に説明しない。

初期化だけの了承を、独自のcommit・push・PR操作への了承に広げない。
初回実装と初期化を一体で行う場合も、当該タスクを受け付けたホストが管理する
作業ブランチ・commit・push・PRフローに従い、独自の外部操作を開始しない。
変更とPRまで明示的に依頼された場合は、当該タスクの作業ブランチとホスト管理のPRフローを使う。
同じ依頼範囲を重ねて確認せず、PRを二重作成しない。
merge・デプロイ・公開・設定変更は別の操作として扱う。
禁止された外部操作とホストの方式が両立しない場合は、変更前に制約を説明して停止する。

cloud agentの利用可否はプラン、権限、組織ポリシー、リポジトリ設定による。
GitHub Actionsの実行時間とAI creditsの消費があるため、実環境での評価は利用者の範囲に従う。
定義ファイルの追加は、契約・権限・設定を有効化する操作ではない。

## スマートフォン

[GitHub Mobileの公式手順](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-mobile)
では、CopilotのNew Sessionからリポジトリを選び、Custom Agentを選択して依頼する手順が示されている。
PRが必要ならプロンプトへ明記する。
Mobileアプリ内でテンプレートからの新規作成まで完結するかは、この確認では確定していない。
新規作成はブラウザーの
[GitHub.comのテンプレート手順](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)
を案内する。スマートフォン実機での通し操作とは区別する。

## ローカルCLIでの発見確認

CLI `1.0.87`のhelpで`copilot skill`の診断入口を確認した。
この配布版のリポジトリのルートで、新しいCLIプロセスから実行する。

```powershell
copilot skill list --json
```

名前、有効状態、リポジトリ配下のパスを照合する。個人版が見つかっただけでは配布版の検出成功にしない。
コマンドがないバージョンではhelpと
[CLIのSkill手順](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-skills)
を確認する。対応する対話CLIでは`/skills reload`、`/skills info aidd-init`、
Agent選択では`/agent`が案内されている。
ローカルでの検出はcloud agentでの起動・Tool解決・PR作成の確認とは別である。

## その他のホスト固有機能

VS Codeの探索・反映は
[Custom Agents](https://code.visualstudio.com/docs/agent-customization/custom-agents)と
[Agent Skills](https://code.visualstudio.com/docs/agent-customization/agent-skills)、
実行ホストの診断で確認する。ローカルの確認をRemote SSH・コンテナー等へ一般化しない。

Hooks、MCP、ホスト専用の委譲設定はこの配布版では追加しない。
必要な場合だけ現行仕様、対象、認証・ネットワーク・副作用、Previewの有無を確認して承認を得る。
イベント名や設定スキーマをホスト間でそのままコピーしない。
Skillの`allowed-tools`は自動承認に関係するため、権限制限のつもりで追加しない。
仕様未確認、ネットワーク制限、Tool不足を設定変更で迂回しない。
