# カスタマイズ成果物の設計

成果物を選んだ後、生成する種類の節だけを読む。
各成果物はこのSkillを導入していないチームメンバーにも理解・利用できるものにする。

## 目次

- [常時読み込む情報](#常時読み込む情報)
- [PR運用方針と完了判定](#pr運用方針と完了判定)
- [製品・設計文書の責務とライフサイクル](#製品設計文書の責務とライフサイクル)
- [開発基盤](#開発基盤)
- [パス別Instructions](#パス別instructions)
- [Custom Agentの設計](#custom-agentの設計)
- [リポジトリ固有Skills](#リポジトリ固有skills)
- [Hooks / MCP は個別判断](#hooks--mcp-は個別判断)

## 常時読み込む情報

`AGENTS.md` は Repository の地図、共通 Instructions は変更時の規則を担当する。

| AGENTS に置く | 共通 Instructions に置く |
| --- | --- |
| 目的、現状、主要な境界と依存方向 | 既存 naming・API・型・例外処理の規約 |
| ソース・テスト・infra の場所 | 変更に対応するテストと回帰防止の要件 |
| Build / Test コマンドと実行ディレクトリ | 秘密、入力検証、権限、外部副作用の規則 |
| 前提 runtime と環境変数の名前 | Definition of Done と未確認事項の明示 |
| 必要時に読む docs / skills の索引 | 既存差分を保つ変更・レビュー方針 |

文書間の相対リンクはリンク元のディレクトリから解決する。
Build / Test の正本は一か所に置き、同じコマンドを大量に複製しない。
長い手順をリンク先へ移しつつ、重要な安全上の制約は適用される入口に短く残す。
初期化の「完了マーカー」は不要。実用的な設定ファイルが存在することを利用する。

## PR運用方針と完了判定

標準初期化、またはAI設定のみでPR方針・完了条件を文書化するときに読む。
PR方針は標準で整える責務だが、固定のファイル一式ではない。

1. 適用されるCONTRIBUTING、PR方針、PR template、branch rules、レビュー要件、CI/CD、
   hosting providerを調べる。既存の正本があれば更新・リンクで補い、同じ規則を別文書へ複製しない。
2. GitHubと確認でき、方針が未指定なら、作業ブランチ→PR→対象head SHAに対応するCI・
   required checksと必要レビュー→承認済みmerge→必要なCD・公開先確認を提案する。
   既存のmain直push運用や組織方針は無断変更しない。GitLab、Azure DevOps等では既存providerの
   レビュー・merge・pipeline運用と配置を使い、GitHub固有の方針ファイルやActionsを押し付けない。
3. 人向けの正本がなくGitHubを使うなら `.github/pull-request-policy.md` を候補にする。
   READMEやCONTRIBUTINGの追記で十分なら分離しない。PR templateは既存物を確認し、
   検証証跡・レビュー観点の記入漏れを防ぐ必要がある場合だけ作成・更新する。
4. 人向け方針に、対象ブランチ、CI・レビュー条件、mergeの承認者と方法、公開の適用条件を置く。
   AGENTSには導線、共通Instructionsには短い完了条件・停止条件と正本へのリンクだけを置く。
   手順の全文やチェックリストを重複管理しない。
5. 分析のみでは書き込まない。「AGENTS.mdだけ」なら既存方針への導線や不足の報告にとどめる。
   AI設定のみでは文書の整備だけを行い、ソース・CI実装・公開を追加しない。
   標準初期化では [development-foundation.md](development-foundation.md) に従って実行可能な検証へつなぐ。

方針の作成はcommit、push、PR作成、merge、デプロイ、公開設定、branch protection / rulesets変更の
自動承認ではない。各操作の依頼範囲・承認・権限・利用可能なToolを確認する。
Instructionsは運用規則であり、権限を付与・制限する仕組みや、実行成功を保証する仕組みではない。

### 段階別の完了条件

初めに依頼の到達点を決め、各段階を成功／未実行／実行中／失敗／承認待ち／対象外として分ける。
コードだけ、ローカル検証まで、PR作成だけ、初期化だけの依頼をmerge・公開まで拡大しない。
対象外は理由を示し、未実行やskippedを成功へ読み替えない。

| 段階 | 確認する証拠 |
| --- | --- |
| ローカル検証 | 対象変更、実行コマンド、作業場所、実行件数と結果。CI定義作成だけではremote CI成功にならない |
| remote CI | 対象PRの最新head SHA、対応するrun URL・SHA・event、期待check jobsとrequired checksの結果、必要レビュー |
| merge | 依頼と既存ルールに沿う承認、checks・レビュー条件、実際のmerge結果とmerge後のcommit SHA |
| CD | 公開対象commit/artifactとの対応、対象環境、必要なdeploy run URLとdeploy jobsの結果 |
| 公開先確認 | 期待する公開URL/API等への必要なsmoke、期待する応答・機能と公開versionの対応 |

公開まで依頼された場合、必要なremote CI・CDと公開先確認がそろうまで公開・納品完了と報告しない。
GitHub Actionsでは、正しい対象commitに対応するrunの `status: completed` と `conclusion: success`
を確認し、さらに期待するcheck/deploy jobsが実際に成功したことを調べる。
queued / in_progress / waitingは未完了、failure / timed_out / cancelledは失敗または未完了とする。
skipped / neutralや期待job・runの欠落も、必要な検証やデプロイが成功した証拠にしない。
run全体がsuccessでも必要なdeploy jobがskippedなら公開完了ではない。
GitHubのmerge許可判定ではskipped / neutralが受理される場合があるが、この完了条件とは区別する。
GitHub上で外部CIを使う既存構成では、GitHubの対象SHAのchecksと提供元のrun・jobsを照合し、
同じterminal successの証拠を求める。GitHub Actionsへの移行は求めない。

PRのhead SHAと検証対象のtest merge SHA / merge queueのSHAが異なる場合は、その対応を確認する。
merge後のCDには実際のmerge / squash等の公開対象SHAを使い、PR時点の成功で代用しない。
新しいcommitや再実行があれば、対象SHAとrun attemptを再確認し、別ブランチ・過去SHAの成功を流用しない。
必要なrunがなければtrigger・対象条件を確認し、承認なしに外部実行を開始しない。
失敗時は該当run・jobのログから原因を確認し、許可範囲の修正・再検証後に判定し直す。
承認・権限・Tool不足、ログや実行結果を読めない場合は「未完了・承認待ち／ブロック」とし、
対象SHA、run URL（取得できなければ未取得）、失敗・未確認の段階、次に必要な操作を返す。
成功を装うskip、`continue-on-error`、checkの無効化や無断の設定変更でゲートを回避しない。
run URLや一回限りの失敗履歴は個別作業の報告に残し、恒久的な運用規則へ埋め込まない。

一次情報（具体的な完了判定を行うときに、提供元の現行仕様と実際の結果を確認する）:
- [GitHub Actions workflow runs: SHA、status、conclusion、run attempt](https://docs.github.com/en/rest/actions/workflow-runs)
- [GitHub Actions workflow jobs: jobごとの結果](https://docs.github.com/en/rest/actions/workflow-jobs)
- [Required status checks: 最新SHA、test merge、skippedの扱い](https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/troubleshooting-required-status-checks)
- [Workflow run history: job・stepのログ確認](https://docs.github.com/en/actions/how-tos/monitor-workflows/view-workflow-run-history)

## 製品・設計文書の責務とライフサイクル

これは Copilot が要求する固定ファイル構成ではない。既存の README、docs、Wiki、RFC、
issue / project の運用を優先し、同じ役割の正本を増やさない。

| 状態 | 代表的な文書 | 責務 | 更新方法 |
| --- | --- | --- | --- |
| 現在の正 | README、`PRODUCT.md`相当、`DESIGN.md`相当、Architecture文書 | 現在の目的、体験、構造、境界、不変条件 | 現状が変わる同じ変更で更新する |
| 判断履歴 | ADR、decision log | 重要な判断の背景、代替案、決定、結果、状態 | 受理済み記録を消さず、変更時は後続記録で置き換える |
| 変更提案・合意仕様 | Plan、Spec、RFC、KEP、Design Doc | 未実装の変更範囲、非目標、受け入れ条件、移行計画 | 提案中と合意済みを区別し、statusと終了条件を持たせる |

文書名は役割を示す例であり必須名ではない。次の条件が実在するときだけ、
既存文書の更新または不足する正本の追加を提案する。

| 文書の役割 | 追加・分離を検討する条件 | 作らない条件 |
| --- | --- | --- |
| Product | 複数の変更でユーザー、目的、非目標、製品制約の解釈が繰り返し必要 | READMEや既存仕様で十分、単一機能の要件だけ |
| Design | UI、ブランド、アクセシビリティ、Design Systemの永続的な原則が複数面を拘束 | 実装詳細、単発画面、技術Architectureだけ |
| Architecture | 複数component / runtime / deployment / trust boundaryの現在状態を共有する必要 | Directory mapだけで理解できる小規模構成 |
| ADR | 戻すコストが高い、複数面を拘束する、または代替案を伴う具体的な判断が成立 | 未決定の案、日常的な実装選択、空の将来用台帳 |
| Plan / Spec / RFC | 長期・複数段階の未実装変更を共有する必要があり、既存Issue等では不足 | Planモードを使っただけ、小さなtask、恒久的な現在仕様、単なる進捗記録 |

標準初期化では、合意仕様と成立した重要判断の保存を提案だけで終えない。
既存の正本がなければ `.github/adr/`、必要時に `.github/architecture.md` を使う。
独立した `.github/plans/` は長期・複数段階の変更で既存Issue・Spec等では不足する場合だけ使う。
承認境界、記録内容、継続更新の手順は [design-records.md](design-records.md) に集約する。
Planモード中はRepositoryへ書き込まず、合意と書き込み許可がそろった実装開始時に保存する。

Specには少なくともstatus、problem、scope、non-goals、acceptance、終了時の移管先を持たせる。
実装完了後は、残る製品要件・Design原則・現在Architectureを現在の正へ、
長期的な判断理由をADRへ移す。Spec自体はRepositoryの既存方針に従い、
履歴として保持、archive、短いredirect化、削除のいずれかを選び、黙って情報を失わない。

AGENTSと共通Instructionsには全文を複製せず、作業に必要な正本と読む条件を示す。
現在の正、判断履歴、未承認の提案を同じ強さで扱わない。

### 必要に応じて分離する補助文書

次の文書は有用だが、すべてのRepositoryに必要な標準セットではない。
READMEや既存文書で十分なら新しいディレクトリを作らない。

| 役割 | 候補パス | 分離を検討する条件 |
| --- | --- | --- |
| 運用・復旧 | `docs/runbooks/`、`docs/operations/` | 定期運用、障害対応、rollback、手動承認を伴う手順を繰り返す |
| Security | `docs/security/`、`docs/threat-model.md` | trust boundary、asset、権限、abuse caseを複数変更で共有する |
| Testing・検証 | `docs/testing.md`、`docs/verification/` | package横断のtest strategy、重い検証、manual acceptance、再現条件がある |
| 公開API・契約 | `docs/api/`、`docs/contracts/` | 外部利用者向けAPI、schema、互換性、versioning方針を維持する |
| Data・migration | `docs/data/`、`docs/migrations/` | schema evolution、ownership、retention、移行・rollback規則がある |
| 開発参加 | `CONTRIBUTING.md`、`docs/development/` | READMEを超えるsetup、review、release、開発環境の手順がある |
| 利用者ガイド | `docs/user-guide/` | READMEのquick startを超える複数の利用Workflowがある |

実行手順には前提、作業場所、入力、検証、失敗時の停止条件を含める。
変化しやすい観測結果や一回限りの証跡を恒久ルールへ混ぜず、必要なら
`docs/verification/`等へ対象version・日付・再現方法とともに分離する。

## 開発基盤

標準初期化では、[development-foundation.md](development-foundation.md) に従って、
テスト、品質ゲート、基本CI、必要なmanifestと文書の不足を補い、実装・検証する。
アプリソースは雛形・機能作成も依頼された場合だけ作る。
「AI設定だけ」「AGENTS.mdだけ」などの明示的な範囲限定は、標準初期化より優先する。
README は人向けの setup/run/test、AGENTS は変更の地図、共通 Instructions は規則という分担を維持する。

## パス別Instructions

`<topic>.instructions.md` に YAML frontmatter と限定した規則を置く。
例のパターンをコピーする前に、実際のファイルと対象外の例で適用範囲を確認する。

```yaml
---
description: このリポジトリのTypeScriptソース規則。
applyTo: "**/*.ts,**/*.tsx"
---
```

`applyTo` の `/` はクライアントの glob 仕様であり、OS のローカルパス区切りとは別。
パスの大小文字、test と production の重なり、generated files、monorepo の package 境界を考慮する。
frontmatter のないファイルをパス一致で常時読み込まれるものとして扱わない。

標準初期化では、小規模・単一言語でも使用言語、テスト、CIの規約を確認し、
既存Instructionsで責務が満たされていなければ `.github/instructions/` に補う。
既存の共通Instructionsに適切な規則があれば複製せず、その正本を維持する。

| 対象 | 記載する規約の例 |
| --- | --- |
| 使用言語・Framework | 命名、型、例外・エラー処理、module/import、Framework固有の作法、lint・formatterとの役割分担 |
| テスト | 配置、命名、runner、fixture、成功・失敗・境界条件、外部I/Oとの分離、変更に対応する回帰確認 |
| CI | 既存の検証入口、runtime・lockfileとの整合、最小権限、action等の参照更新、secrets・CDとの境界 |

Pythonなら採用バージョンの公式スタイル・型・テストの資料、TypeScriptなら公式の型・moduleと
採用Framework・lint presetの資料など、実際の技術に合う一次情報を確認する。
既存設定を優先し、必要な規則と根拠への短いリンクを記す。一般的なスタイルガイド全文をコピーしない。
自動検査できる書式を長文の指示で重複定義せず、対応するTool設定を正本にする。
未使用言語や実体のない対象、差分ルールのない項目のために空ファイルを作らない。

## Custom Agentの設計

### 適性評価と作成の判断

承認済みの初期化・AI設定更新では、Agentを作らない場合も適性評価を省略しない。
実装、既存手順、ユーザーの要件から次の条件を確認する。
評価のために未指定の会話履歴や内部DBを収集したり、リポジトリ全体を読み直したりしない。

| 条件 | 確認すること |
| --- | --- |
| 独立した責務 | Mainと異なる判断・成果があり、一続きの調査を機械的に分割したものではない |
| 反復需要 | 同じ専門判断を繰り返す根拠があり、一度きりの作業や将来の可能性だけではない |
| 既存との差 | 組み込み・既存Agentや共通Instructionsだけでは不足する、再利用可能な専門性・境界がある |
| 入出力と完了条件 | 渡す対象・前提・証拠、返す成果、担当外、入力不足時の停止条件を具体化できる |
| 権限と対応状況 | 必要最小限のToolで実施でき、対象ホストで発見・利用を確認できる。未承認の権限拡大に依存しない |
| 分離の効果 | 文脈分離や独立した確認の利点が、起動・引き継ぎ・重複調査・統合の負担に見合うと判断できる |

候補ごとに「再利用／更新／作成／見送り／確認待ち」と根拠を短く示す。
条件を満たす不足役割が許可範囲にあれば、実際の定義と導線を作成し、提案だけで終えない。
既存Agentが同じ責務を満たすなら再利用し、参照先や入出力だけが古ければその定義を更新する。
Tool一覧が同じでも専門判断と入出力が異なる役割はあり、Toolの一致だけを見送りの理由にしない。
小規模でも条件を満たせば作成し、複雑でも根拠がなければ増やさない。Agent数を成果にしない。
分析のみ・単一ファイル指定等では評価結果だけを返し、指定外のAgentファイルを作らない。
新しい根拠がなければno-opとし、判断のためだけの台帳・完了マーカーは作らない。

### 定義と運用への接続

`.github/agents/<role>.agent.md` を使う。共有可能な最小例:

```yaml
---
name: architect
description: アーキテクチャと変更影響を分析し、範囲を限定した実装計画を返す。
tools: ['read', 'search']
---
```

本文に担当範囲、対象外、必要な入力、返す成果、停止条件、他 Agent との引き継ぎを明記する。
新規Agentは既存名との衝突を避け、更新では既存名と利用者の追記を保持する。
必要のない model 指定、ホスト専用 frontmatter、旧 `infer` は加えない。
`description`には専門性だけでなく使う条件を記す。AGENTSに入口を、共通Instructionsに
Mainからの選択条件・入力・結果統合の責任を短く置き、詳細な規則の本文は複製しない。
作成済みAgentを常に起動する必要はない。設定の存在、名前指定での起動、自動選択の確認を分ける。
自動選択はモデル・ホストに依存し、キーワードによる確実な振り分けや無人のhandoffとは説明しない。

| 役割 | 最初に検討する権限 | 返却物・境界 |
| --- | --- | --- |
| 設計担当 | `read`, `search` | 根拠つき設計、影響範囲、実装手順。編集・実行なし |
| 実装担当 | `read`, `search`, `edit`。必要なら `execute` | 許可範囲の差分と未解決事項 |
| テスト担当 | `read`, `search` と実在する検証Tool。必要なら `edit`, `execute` | 受け入れ条件に対応するテストと実行結果 |
| レビュー担当 | 原則 `read`, `search` | 正しさと回帰の根拠・指摘。実装変更なし |
| セキュリティレビュー担当 | 原則 `read`, `search` | 信頼境界に沿った指摘。スキャンや外部送信は別承認 |

この表は自動生成する一式ではない。Tool 名・alias が対象クライアントで有効かを確認する。
実行専用の検証 Tool がない場合、Reviewer に shell を追加するより Tester の結果を渡す。
`execute` は「テストだけ許可する sandbox」ではない。追加すると汎用コマンド実行能力になる。
同様に `agent` は他の権限を持つ子への委譲につながるため、読み取り専用 Agent に安易に加えない。
`tools` の省略や `*` を最小権限として扱わない。Skill の `allowed-tools` は CLI では自動承認に
関わるため、権限を制限するつもりで設定しない。

Main を組み込み Agent で運用できれば、専用 orchestrator ファイルを増やさない。
VS Code で `agents` allowlist や `handoffs` が必要な場合だけ、現在の schema と実在する Agent 名を確認する。
`agents` を CLI 共通のアクセス制御、`handoffs` を無人で必ず順番に実行する仕組みとして説明しない。
他ホストと共有できないものは、対応している場合に `target: vscode` 等で対象を限定する。

### 継続更新

次の変化が確認できたときだけ、関係する既存Agentを見直す。

| 変化 | 見直す対象 |
| --- | --- |
| 構造・契約・検証入口の変更 | 対象パス、参照文書、入力として必要な検証結果、確認観点 |
| 誤委譲・入力不足・重複調査が実際に発生 | `description`、担当境界、引き継ぐ前提、返却形式、停止条件 |
| ホスト・Toolの対応変更 | 発見方法、解決するTool名、利用可能な機能と未確認範囲 |
| 責務が重複、または利用する根拠がなくなった | 再利用・統合・廃止の要否。削除や責務を失う統合は影響を示して確認する |

承認されたAI設定更新の範囲で、関係する定義と導線だけを更新する。
通常の機能依頼やAI設定の変更を含まない依頼では、必要な見直しを報告するに留める。
生成先のInstructionsには、この見直し条件と許可範囲を短く残す。
参照先・説明・入出力の保守と、Tool追加・`execute`や`agent`の付与・委譲範囲拡大を区別する。
後者やHooks・MCP・外部接続・自動承認の変更は、対象と影響への承認なしに行わない。

更新後は発見と代表ケースを確認し、担当外・入力不足で未確認を返せることも確かめる。
最小権限・既存名・ユーザーの追記を保持し、失敗を回避するための権限拡大はしない。
時間や利用量を測っていない場合は高速化・低コスト化を実証済みとせず、
変化がなければ定義や日時を更新しない。自動的にAgentを増殖・自己改変させる仕組みは作らない。

## リポジトリ固有Skills

`.github/skills/<workflow>/SKILL.md` に `name` と具体的な `description` を持たせる。
name は小文字英数字とハイフン、64 文字以内、ディレクトリ名と一致させる。
description は 1024 文字以内を目安ではなく制約として守る。

一つの Skill に一つの反復作業を担当させる。例:

- 既存手順に従う Deployment: 環境の特定、dry-run、承認、本番操作、結果確認、rollback。
- Migration: backup、互換性、dry-run、対象データ確認、承認、適用、検証。
- Evaluation: dataset の取扱い、基準、実行、比較、未達時の停止。
- Environment Setup: 宣言された runtime、環境変数の名前、既存の install 手順、疎通確認。

これらの見出しだけの Skill は作らない。コマンドや前提が実在し、同じ作業を繰り返す理由が必要。
長い参照は `references`、必要な決定的処理は `scripts` へ分離する。
追加 script には必要なエラー処理と検証を用意し、Global 個人パスや秘密値を埋め込まない。
依存の自動インストール、deployment の自動承認、破壊的操作の追認を組み込まない。

## Hooks / MCP は個別判断

Hooks はイベントで実行される処理、MCP は外部 System への Tool 接続。
どちらも Instructions / Skills の代わりではなく、初期化の必須要素ではない。
生成・変更する前に [client-support.md](client-support.md) の対応差を確認する。

| 機能 | 作成前に必要なこと |
| --- | --- |
| Formatting / lint hook | 既存コマンド、変更対象だけの処理、再帰実行回避、元の差分保護、対応イベント |
| Policy / dangerous command hook | 入出力 schema、拒否時・失敗時・timeout 時の挙動、許可／拒否テスト、保護方法 |
| MCP | 必要な server / Tool、取得・送信するデータ、認証方式、対象環境、最小権限 |

具体的なスクリプトや接続先、その副作用と Preview 状態を提示して承認を得る。
「AI 設定を初期化する」了承から、接続、インストール、Tool 自動承認、権限付与を推論しない。
承認前は説明にとどめ、自動読み込みされる場所に実行可能な設定ファイルを置かない。
同じ hooks ディレクトリを読むクライアントに、互換性未確認の二種類の設定を並べない。

必須チェックは、可能なら既存の CI gate に接続し、Agent の記憶だけに頼らない。
ただし hook や CI もホスト権限・管理者ポリシーの代替ではない。
エラーを握り潰した成功、timeout を安全な拒否とみなす実装、無検証の deny regex を作らない。
