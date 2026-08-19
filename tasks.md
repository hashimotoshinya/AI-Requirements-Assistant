TASKS.md

AI Requirements Assistant

Version 1.2 開発タスク

進捗記号: `[ ]` 未着手 / `[~]` 作業中 / `[x]` 完了 / `[!]` ブロック

各Phaseは、実装・記載されたテスト・完了条件をすべて満たした時点で`[x]`に更新する。作業内容、テスト結果、判断事項は`worklog.md`へ記録する。

⸻

## [!] Phase 0: プロジェクト準備

* Git初期化
* GitHubリポジトリ作成
* Laravelプロジェクト作成
* Docker Compose構築
* MySQL構築
* .env.example整理
* README.md
* AGENTS.md
* spec.md
* TASKS.md
* 初期コミット

テスト:

* Laravel起動確認
* DB接続確認

完了条件:

Laravel + Docker + MySQLが正常稼働。

⸻

## [ ] Phase 1: 認証

* Register
* Login
* Logout
* Auth Middleware

テスト:

* Register Feature Test
* Login Feature Test
* 未認証アクセス拒否

完了条件:

認証済みユーザーのみアプリ利用可能。

⸻

## [ ] Phase 2: Project

* projects migration
* Project model
* User relation
* Project Policy
* 一覧
* 作成
* 詳細
* 編集
* 削除

テスト:

* CRUD Feature Test
* 他ユーザーProjectアクセス拒否

⸻

## [ ] Phase 3: Conversation基盤

* conversations migration
* SoftDeletes
* Conversation model
* Project relation
* speaker
* content
* source_type
* source_reference
* spoken_at

テスト:

* Model relation
* Soft Delete

⸻

## [ ] Phase 4: Conversation直接入力

* speaker選択
* content入力
* 保存
* 時系列表示
* 編集
* 論理削除

テスト:

* Create
* Update
* Soft Delete
* 他Project操作拒否

⸻

## [ ] Phase 5: ConversationRevision

* conversation_revisions migration
* ConversationRevision model
* 編集時before保存
* 編集時after保存
* changed_by_user_id

テスト:

* 編集時Revision生成
* 複数Revision保持

⸻

## [ ] Phase 6: RuleBasedConversationSegmenter

* ConversationSegmenterInterface
* RuleBasedConversationSegmenter
* speaker label dictionary
* 改行パターン処理
* 全角コロン対応
* 英語speaker対応
* 未認識speakerをunknownにする

テスト:

* 顧客/自分
* Client/Developer
* 全角コロン
* 複数行発言
* speakerなし

⸻

## [ ] Phase 7: 一括貼り付けUI

* Paste UI
* 分割実行
* Preview
* speaker編集
* content編集
* 行削除
* 一括保存
* 単一Conversation保存選択肢

テスト:

* 複数Conversation保存
* Preview修正反映
* 分割失敗時処理

⸻

## [ ] Phase 8: AI Provider

* OpenAI設定
* API Key .env
* AIProviderInterface
* OpenAIProvider
* AnalysisInput DTO
* AnalysisResult DTO
* Timeout
* API Error
* Mock Provider

テスト:

* Provider Unit Test
* Timeout処理
* API Error処理

⸻

## [ ] Phase 9: Analysis基盤

* analyses migration
* Analysis model
* project_id
* model
* prompt_version
* status
* raw_response
* parsed_response
* current_requirements_snapshot
* error_message
* started_at
* completed_at

テスト:

* Status transition
* Snapshot保存

⸻

## [ ] Phase 10: AnalysisConversation

* analysis_conversations migration
* AnalysisConversation model
* Analysis relation
* Conversation relation
* context_type

context_type:

target
context

テスト:

* target保存
* context保存
* 複数Conversation紐付け

⸻

## [ ] Phase 11: AnalysisService

* AnalysisService
* Project context構築
* Target Conversation取得
* Context Conversation取得
* Current Requirements snapshot構築
* Prompt構築
* AI Provider呼び出し
* Analysis status更新
* raw_response保存
* parsed_response保存
* Error処理

Version 1ではRAGなし。

テスト:

* 正常解析
* Provider failure
* Snapshot利用
* AnalysisConversation保存

⸻

## [ ] Phase 12: Schema Validation

検証:

* JSON
* 必須キー
* Enum
* 型
* confidence
* 最大文字数
* 最大件数
* Conversation ID

テスト:

* 正常JSON
* 不正JSON
* 不正Enum
* 不正ID
* 過大レスポンス

完了条件:

不正AI responseが業務データへ反映されない。

⸻

## [ ] Phase 13: RequirementSuggestion

* requirement_suggestions migration
* Model
* analysis_id
* category
* title
* description
* ai_judgement
* confidence
* status

ai_judgement:

suggested_confirmed
candidate
unresolved

status:

pending
accepted
rejected

テスト:

* AI解析結果から生成
* suggested_confirmed保存
* pending初期状態

⸻

## [ ] Phase 14: Requirement

* requirements migration
* Requirement model
* category
* title
* description
* status
* created_by
* confirmed_by
* confirmed_at

status:

candidate
confirmed
rejected

テスト:

* Requirement作成
* Candidate作成
* ConfirmedをAIが直接作成できないこと

⸻

## [ ] Phase 15: Suggestion承認フロー

* 確定として採用
* Candidateとして採用
* 編集して採用
* 却下
* Suggestion status更新

テスト:

* accepted → confirmed
* accepted → candidate
* rejected
* AI側からconfirmed不可

⸻

## [ ] Phase 16: RequirementEvidence

* requirement_evidences migration
* Model
* requirement_id
* conversation_id
* analysis_id
* quoted_text
* evidence_type
* 複数Evidence

テスト:

* direct
* supporting
* contradicting
* 複数Conversation
* Conversation編集後もquoted_text保持

⸻

## [ ] Phase 17: RequirementRevision

* requirement_revisions migration
* Model
* before_data
* after_data
* change_type
* changed_by_user_id

テスト:

* Edit revision
* Confirm revision
* Reject revision

⸻

## [ ] Phase 18: RequirementIssue

* requirement_issues migration
* Model
* category
* title
* description
* reason
* priority
* status

テスト:

* AI解析から生成
* resolved
* dismissed

⸻

## [ ] Phase 19: SuggestedQuestion

* suggested_questions migration
* Model
* RequirementIssue relation
* question
* reason
* priority
* status

テスト:

* IssueとQuestion別保存
* asked
* resolved
* dismissed

⸻

## [ ] Phase 20: Term

* terms migration
* Model
* term
* description
* context_description
* UI

テスト:

* 保存
* 同一Analysis重複対策

⸻

## [ ] Phase 21: Risk

* risks migration
* Model
* title
* description
* severity
* status

テスト:

* low
* medium
* high
* resolve / dismiss

⸻

## [ ] Phase 22: RequirementChangeProposal

* requirement_change_proposals migration
* Model
* requirement_id
* analysis_id
* proposed_category
* proposed_title
* proposed_description
* reason
* confidence
* status

status:

pending
accepted
rejected

テスト:

* Proposal生成
* confirmed Requirementが直接更新されない

⸻

## [ ] Phase 23: ChangeProposal適用

* Accept操作
* Reject操作
* Requirement更新
* RequirementRevision生成
* Evidence追加
* Proposal status更新

テスト:

* accepted
* rejected
* Revision生成
* confirmed自動変更禁止

⸻

## [ ] Phase 24: 再解析

* 新規target Conversation識別
* context Conversation選択
* Current Requirement Snapshot
* 既存Requirement考慮
* 新Suggestion生成
* Evidence追加候補
* Issue
* Question
* Risk
* ChangeProposal

テスト:

* 既存confirmed維持
* ChangeProposal生成
* 同一Requirement重複防止

⸻

## [ ] Phase 25: 重複対策

Version 1簡易判定:

* project_id
* category
* normalized title
* status

Embeddingは使わない。

テスト:

* 完全一致
* 大文字小文字
* 空白正規化

⸻

## [ ] Phase 26: Feedback

* feedback migration
* Model
* useful
* unnecessary
* corrected
* target_type
* target_id

テスト:

* 保存
* 対象紐付け

⸻

## [ ] Phase 27: Analysis排他制御

* 同一Project processing確認
* processing中の新規Analysis拒否
* HTTP 409
* UIエラー表示

テスト:

* 2重開始拒否
* 別Projectは許可
* failed後再試行

⸻

## [ ] Phase 28: Rate Limit

* Laravel RateLimiter
* User単位制限
* 初期値5回/分候補
* 設定可能化

テスト:

* 上限以内
* 上限超過
* 時間経過後再実行

⸻

## [ ] Phase 29: AI安全制御

* 入力文字数上限
* Timeout
* Loading
* Button disable
* Error表示
* Retry
* API失敗時DB保護

テスト:

* 入力超過
* Timeout
* API failure
* 二重送信

⸻

## [ ] Phase 30: 外部AI送信表示

* AI送信表示
* 必要なら初回同意
* Privacy説明
* API Key露出確認
* Log確認

表示例:

入力した会話内容は解析のため外部AIサービスへ送信されます。

⸻

## [ ] Phase 31: Project Detail UI

* Conversation Panel
* Manual Input
* Paste Input
* AI Analyze
* Requirement Suggestions
* Requirement Issues
* Suggested Questions
* Terms
* Risks
* Official Requirements
* Change Proposals
* Loading
* Errors

⸻

## [ ] Phase 32: Requirement UI

* ai_judgement表示
* confidence表示
* Evidence表示
* Confirm
* Candidate
* Edit
* Reject
* Revision
* ChangeProposal

⸻

## [ ] Phase 33: セキュリティ確認

* API Key Git非含有
* API Key HTML非露出
* Authorization
* Validation
* CSRF
* Mass Assignment
* Soft Delete
* Conversation通常ログ非出力
* raw_response通常ログ非出力

テスト:

* 他ユーザーアクセス
* 不正ID
* CSRF
* Validation

⸻

## [ ] Phase 34: 統合・回帰テスト

ここでは各PhaseのUnit / Feature Testを再実行する。

* 全Unit Test
* 全Feature Test
* AI Provider Mock Test
* Regression Test

このPhaseで初めてテストを書くのではなく、既存テストの総合確認を行う。

⸻

## [ ] Phase 35: Version 1受入テスト

入力:

顧客：
予約システムを作りたいです。
開発者：
ログイン機能は必要ですか？
顧客：
はい。
メールアドレスとパスワードでお願いします。
顧客：
店舗を選んで日時を予約できるようにしたいです。
顧客：
決済はStripeを考えています。

確認:

* 一括貼り付け
* Rule Based分割
* Preview編集
* Conversation保存
* AI解析
* AnalysisConversation保存
* Requirements Snapshot保存
* RequirementSuggestion生成
* メールログインがsuggested_confirmed
* Suggestionはpending
* 正式Requirementは自動confirmedされない
* ユーザー操作でconfirmed可能
* Evidence保存
* RequirementIssue生成
* SuggestedQuestion生成
* Term表示
* Risk表示
* raw_response保存
* parsed_response保存

⸻

## [ ] Phase 36: 再解析受入テスト

追加:

顧客：
StripeではなくPayPalに変更するかもしれません。

確認:

* 新Conversation保存
* 新Analysis作成
* 以前のRequirement Snapshot保存
* Stripe Requirement自動変更なし
* ChangeProposal生成
* contradicting Evidence
* ユーザーReject可能
* ユーザーAccept可能
* Accept時Revision生成

⸻

## [ ] Phase 37: 排他・コスト受入テスト

* 同一Project同時解析拒否
* 別Project解析可能
* Rate Limit機能
* Button連打対策
* API Timeout
* Retry
* API失敗後も正式Requirement保持

⸻

## [ ] Phase 38: Version 1完成条件

以下が成立する。

Conversation
 ↓
Analysis
 ↓
RequirementSuggestion
 ↓
Human Review
 ↓
Requirement
 ↓
Additional Conversation
 ↓
Re-analysis
 ↓
ChangeProposal
 ↓
Human Review

さらに、

Analysis → Input Conversations追跡可能
Requirement → Evidence追跡可能
Conversation → Revision追跡可能
Requirement → Revision追跡可能

であること。

⸻

Version 2拡張確認

* source_type=chat追加可能
* Conversation単位で逐次保存可能
* AnalysisService再利用可能
* Requirement系変更不要
* Async Queueへ移行可能

⸻

Version 3拡張確認

Speech
 ↓
Transcript
 ↓
Conversation

* SpeechProvider追加可能
* spoken_at利用可能
* source_reference利用可能
* AnalysisService変更不要

⸻

Version 4拡張確認

Meeting Adapter
 ↓
Conversation
 ↓
Analysis

* Zoom固有処理分離
* Meet固有処理分離
* Teams固有処理分離
* Meeting IDをsource_referenceで保持可能
* speaker拡張可能

⸻

Version 1では実装しない

* AI会話分割
* RAG
* Vector DB
* Speech
* Zoom
* Meet
* Teams
* AI自動学習
* Requirement自動confirmed
* confirmed Requirement自動更新

⸻

最終確認原則

実装時、常に以下を確認する。

これはAIの提案か？
それとも正式要件か？
このAI解析は何を入力として使ったか追跡できるか？
この要件は何を根拠として確定されたか追跡できるか？
過去の根拠や人間の判断をAIが上書きしていないか？

Version 1.2は、

AIを賢くすることだけではなく、AIの提案・根拠・履歴を人間が安全に管理できること

を完成条件とする。
