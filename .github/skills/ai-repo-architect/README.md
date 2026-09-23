# ai-repo-architect

リポジトリを分析し、了承された範囲でAI設定と開発基盤の不足を補うSkillです。
標準初期化では、使用言語の規約、PR運用方針、意味のあるテスト、
適用できる品質ゲート、基本CI、必要な設計記録までを扱います。
既存の構成と利用者の差分を保持し、アプリソースは雛形・機能作成も依頼された場合だけ追加します。

## 使い方

このフォルダー全体を対象リポジトリの`.github/skills/ai-repo-architect`に置き、
Copilotへ「同梱のai-repo-architectを使って、このリポジトリを標準初期化してください」と依頼します。
用途、使用技術、許可範囲を併記してください。初期化の了承なくファイルは変更しません。

「分析だけ。変更しないで」「AI設定だけ」「AGENTS.mdだけ」などの指定は、
標準初期化より優先します。ファイルの存在やAgentの選択だけでは作業を開始しません。
個人PCのInstruction、別の初期化Skill、インストーラー、専用MCPを前提にしません。

CLIで同名の個人Skillも使っている場合は、読み込まれる実体のパスを確認し、
対象リポジトリの`SKILL.md`を明示してください。
対応するCLIでは、新しいプロセスをリポジトリのルートで起動して一覧を確認できます。

```powershell
copilot skill list --json
```

`ai-repo-architect`という名前だけでなく、パスがこのリポジトリ配下であることと、
有効になっていることを確認します。この診断コマンドの提供状況はCLIのバージョンで確認してください。
詳細は[クライアント対応](references/client-support.md)を参照してください。

## 手順と参照資料

手順の正本は[SKILL.md](SKILL.md)です。参照資料は必要な段階だけ読みます。

| ファイル | 読む条件 |
| --- | --- |
| [analysis-and-sizing.md](references/analysis-and-sizing.md) | 既存構成、規模、技術・運用の境界を調べるとき |
| [artifact-design.md](references/artifact-design.md) | Instructions・Agents・Skills・PR方針等を選び、生成するとき |
| [client-support.md](references/client-support.md) | 実行ホストの機能と対応差を確認するとき |
| [development-foundation.md](references/development-foundation.md) | 標準初期化でテスト・品質ゲート・基本CIを整えるとき |
| [design-records.md](references/design-records.md) | 合意仕様や重要な設計判断を記録するとき |
| [evals.json](evals/evals.json) | Skillを変更し、承認境界や生成範囲の振る舞いを評価するとき |

## 評価と保守

`evals.json`はプロンプト、期待結果、確認項目のデータであり、
ファイルを置いただけで評価が実行される仕組みではありません。
ケースごとに前提を満たす隔離された作業用リポジトリを用意し、出力と変更範囲を確認します。
評価のために実リポジトリへcommit・push・デプロイしたり、外部サービスを作ったりしないでください。
クラウド固有ケースは、静的な判断確認と実際のcloud agent実行を分けて記録します。

更新時にはfrontmatter、ディレクトリ名と`name`、相対リンク、同梱資料、ライセンスを確認します。
代表ケースに加え、分析のみ・入力不足・担当外・同じ要件での再実行を確認してください。
未実行のケースやクラウドでの未確認事項は、成功した評価と区別します。

個人版とこの配布版を自動同期する仕組みはありません。
更新は意図した変更だけをレビューして取り込み、利用者の追記と承認境界を保持します。
実装を伴わない互換性メモを、全クライアントでの実行保証に読み替えないでください。

## 出典とライセンス

matayuuuが管理する`ai-repo-architect`を基に、個人環境向けのREADME・互換性メモを
リポジトリ配布向けに変更し、cloud agentの作業境界と評価ケースを追加しています。
元の分析・開発基盤・設計記録の手順と既存評価ケースを引き継いでいます。

Copyright (c) 2026 matayuuu。[MIT License](LICENSE)で改変・再配布できます。
このフォルダーを単独で配布する場合も、`LICENSE`の著作権表示と許諾表示を含めてください。
