AGENTS.md

AI Requirements Assistant

Version 1.2

⸻

1. プロジェクト概要

本プロジェクトは、開発案件における顧客・開発者間の会話をAIで解析し、要件定義を支援するWebアプリケーションを開発するものである。

Version 1では、ユーザーが会話テキストを直接入力、または複数話者を含む会話ログを貼り付けし、その内容をAIが解析する。

AIは以下を提案する。

* 要件候補
* 確定可能性が高い要件候補
* 未確認事項
* 次に確認すべき質問
* 専門用語と解説
* リスク
* 矛盾
* 既存要件への変更提案

本プロジェクトは将来的に以下へ拡張する。

* Version 2: チャット形式による逐次入力・解析
* Version 3: 音声入力・文字起こし
* Version 4: Zoom / Google Meet / Teams等との連携およびリアルタイム解析

Version 1の段階から、入力方式・AIサービス・オンライン会議サービスに依存しすぎない設計とする。

⸻

2. 最重要原則

本プロジェクトでは以下を最重要原則とする。

AIは分析・判断・提案を行う。
正式な要件の確定・変更・却下は人間が行う。

AI解析結果と正式な業務データを混同しない。

処理は原則として以下とする。

Conversation
    ↓
AI Analysis
    ↓
AI Suggestions
    ↓
Human Review
    ↓
Official Requirements

AIが正式なRequirementを直接confirmedに変更してはならない。

既存のconfirmed要件をAIが直接更新してはならない。

⸻

3. Version 1の目的

Version 1では以下を達成する。

1. 会話を案件単位で保存する
2. 会話からAIが要件候補を抽出する
3. AI提案を正式要件と分離して保持する
4. 要件の根拠となる発言を追跡できる
5. 未確認事項を抽出する
6. 顧客へ次に聞く質問を提案する
7. 専門用語を解説する
8. リスク・矛盾を検出する
9. 要件変更候補を提示する
10. 人間が正式要件を確定・修正・却下できる
11. 変更履歴を追跡できる
12. AI解析時に何を入力したか追跡できる
13. 将来的なリアルタイム会話解析へ拡張可能にする

⸻

4. Version 1のスコープ

実装する

* ユーザー認証
* 案件管理
* Conversation管理
* 会話直接入力
* 会話一括貼り付け
* ルールベース会話分割
* 分割結果プレビュー・修正
* Conversation論理削除
* Conversation改訂履歴
* AI解析
* AI解析履歴
* AI解析対象Conversation記録
* RequirementSuggestion
* Requirement
* RequirementEvidence
* RequirementRevision
* RequirementChangeProposal
* RequirementIssue
* SuggestedQuestion
* Term
* Risk
* Feedback
* AI解析の案件単位排他制御
* ユーザー単位レート制限
* AI送信同意表示
* APIエラー・タイムアウト対応

Version 1では実装しない

* 音声入力
* 音声文字起こし
* Zoom連携
* Google Meet連携
* Teams連携
* ストリーミング解析
* 完全リアルタイム解析
* RAG
* Vector DB
* AIモデルのファインチューニング
* AIによる自動学習
* AIによる正式要件の自動確定
* AIによるconfirmed要件の自動更新
* 複数ユーザー共同編集
* GitHub / Notion / Jira等への自動登録

⸻

5. 技術構成

Version 1の基本構成は以下とする。

Backend:

* Laravel

Frontend:

* Blade
* 必要に応じてAlpine.js

Database:

* MySQL

AI:

* OpenAI API

Environment:

* Docker Compose

Authentication:

* Laravel標準認証機構

Source Control:

* Git
* GitHub

Version 1ではReact / Vueを必須としない。

将来的なSPA化・リアルタイム化を阻害しないよう、Controller・Service・Providerの責務を分離する。

⸻

6. 将来拡張の基本構造

入力元に関係なく最終的にConversationへ変換する。

Version 1
Manual / Paste
      ↓
Conversation
Version 2
Chat
 ↓
Conversation
Version 3
Microphone
 ↓
Speech Provider
 ↓
Transcript
 ↓
Conversation
Version 4
Zoom / Meet / Teams
 ↓
Meeting Adapter
 ↓
Transcript
 ↓
Conversation

AI解析処理はConversation以降を共通利用する。

⸻

7. Conversation設計原則

Conversationは原則として、

1話者・1発言

単位で保存する。

例:

Conversation 1
speaker = customer
content = "予約システムを作りたいです"
Conversation 2
speaker = developer
content = "ログイン機能は必要ですか？"
Conversation 3
speaker = customer
content = "必要です"

⸻

8. 会話一括貼り付け

Version 1では複数話者を含む会話ログを一括貼り付けできる。

例:

顧客：
予約サイトを作りたいです。
自分：
ログイン機能は必要ですか？
顧客：
はい。必要です。

処理:

Paste
 ↓
RuleBasedConversationSegmenter
 ↓
分割候補
 ↓
プレビュー
 ↓
ユーザー修正
 ↓
保存

Version 1ではAIによる会話分割を原則使用しない。

ルール例:

* 顧客:
* 顧客：
* クライアント:
* 自分:
* 開発者:
* 担当者:
* Customer:
* Client:
* Developer:
* Me:

等。

ルールで分割できない場合は、単一Conversationとして保存する選択肢を提供する。

将来的には以下へ拡張可能とする。

ConversationSegmenterInterface
 ├─ RuleBasedConversationSegmenter
 └─ AIConversationSegmenter

ただしVersion 1でAIConversationSegmenterを実装しない。

⸻

9. Conversationの編集・削除

ConversationはRequirementEvidenceやAnalysisの根拠となるため、履歴保全を優先する。

削除

物理削除を原則禁止する。

Laravel SoftDeletes を使用する。

編集

Conversation編集時はConversationRevisionを作成する。

最低限保持する。

* conversation_id
* before_content
* after_content
* before_speaker
* after_speaker
* changed_by_user_id
* created_at

RequirementEvidenceには解析時点のquoted_textを保持する。

Conversationが後から変更されても、過去解析時の根拠を失わないこと。

⸻

10. AI Provider

ControllerからOpenAI APIを直接呼び出してはならない。

構造:

Controller
 ↓
AnalysisService
 ↓
AIProviderInterface
 ↓
OpenAIProvider

Version 1の主要契約:

AIProviderInterface
analyzeConversation(AnalysisInput $input): AnalysisResult

将来的に交換可能:

* OpenAIProvider
* GeminiProvider
* LocalLLMProvider

⸻

11. AnalysisInput

AIへ渡す情報を明示的な入力オブジェクトへまとめる。

Version 1では最低限以下を含む。

* Project context
* 今回解析対象Conversation
* 必要な過去Conversation
* Current Requirements snapshot
* 固定解析ルール
* Prompt version

Version 1ではRAGによる外部Knowledge取得は行わない。

⸻

12. Analysis履歴

AI解析実行ごとにAnalysisを保存する。

最低限保持する。

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

⸻

13. AnalysisとConversationの関係

解析時にどのConversationを利用したか追跡可能にする。

analysis_conversations中間テーブルを使用する。

例:

analysis_conversations
id
analysis_id
conversation_id
context_type
created_at

context_type例:

target
context

target:
今回主に解析する新しいConversation。

context:
文脈維持のため送信した過去Conversation。

これにより後から、

このAnalysisは何を入力として解析したのか

を追跡可能にする。

⸻

14. Current Requirements snapshot

Analysis実行時の既存Requirement状態をJSONスナップショットとして保存する。

目的:

* 再現性
* デバッグ
* 重複判定検証
* AI変更提案の監査

Version 1ではRequirement専用Snapshotテーブルを必須とせず、

analyses.current_requirements_snapshot

で保持してよい。

⸻

15. raw_response

AI raw responseは保存可能。

目的:

* デバッグ
* 障害調査
* AI解析再現
* Prompt改善
* 監査

ただし以下を禁止する。

* raw_responseを正式業務データとして扱う
* raw_responseをそのままRequirementとして保存する
* raw_responseを通常画面の正本として表示する
* raw_responseを通常ログへ出力する

処理:

AI
 ↓
raw_response
 ↓
Schema Validation
 ↓
DTO / ParsedResult
 ↓
Application Logic

⸻

16. RequirementSuggestion

AIが生成した要件提案を、正式Requirementとは別に保存する。

requirement_suggestionsを使用する。

最低限:

* project_id
* analysis_id
* category
* title
* description
* ai_judgement
* confidence
* status
* created_at
* updated_at

ai_judgement:

suggested_confirmed
candidate
unresolved

status:

pending
accepted
rejected

AI提案の原本として保持する。

⸻

17. RequirementSuggestionとRequirement

AI提案をそのままRequirementへ自動昇格させない。

処理:

RequirementSuggestion
       ↓
Human Review
       ├─ Confirm
       ├─ Keep as Candidate
       ├─ Edit
       └─ Reject

ユーザー操作によってRequirementを作成・更新する。

RequirementSuggestionとRequirementは別ドメインとする。

⸻

18. Requirement

正式な案件要件を管理する。

status:

candidate
confirmed
rejected

AIがRequirement.statusを直接confirmedへ変更することは禁止する。

AIがsuggested_confirmedと判断しても、それはRequirementSuggestion上のAI判断に過ぎない。

⸻

19. RequirementEvidence

Requirementは複数Conversationを根拠にできる。

Requirement
    ↓
RequirementEvidence
    ↓
Conversation

最低限:

* requirement_id
* conversation_id
* analysis_id
* quoted_text
* evidence_type
* created_at

evidence_type:

direct
supporting
contradicting

単一のsource_conversation_idだけに依存する設計は禁止する。

⸻

20. RequirementRevision

Requirement変更履歴を保存する。

最低限追跡する。

* title変更
* description変更
* category変更
* status変更
* confirmed
* rejected
* ChangeProposal適用

最低限:

* requirement_id
* changed_by_user_id
* change_type
* before_data
* after_data
* created_at

⸻

21. RequirementChangeProposal

AIが既存Requirementの変更可能性を検出した場合、Requirementを直接更新しない。

必ずRequirementChangeProposalを生成する。

status:

pending
accepted
rejected

例:

Current:
Stripe
New Conversation:
「PayPalに変えるかもしれません」
↓
Change Proposal
Current:
Stripe
Proposed:
PayPal
Evidence:
該当Conversation
↓
Human Review

confirmed要件への変更は必ずユーザー承認を必要とする。

⸻

22. RequirementIssue

未確認事項を管理する。

RequirementIssueは、

要件定義上、決まっていない事項

を表す。

例:

予約キャンセル仕様が未決定

status:

open
resolved
dismissed

⸻

23. SuggestedQuestion

SuggestedQuestionは、

RequirementIssueを解消するために実際に顧客へ聞く質問

を表す。

例:

RequirementIssue:
予約キャンセル仕様が未決定
SuggestedQuestion:
「ユーザー自身で予約をキャンセルできますか？」

status:

pending
asked
resolved
dismissed

RequirementIssueとSuggestedQuestionを混同しない。

⸻

24. Term

専門用語を保存する。

最低限:

* term
* description
* context_description
* project_id
* analysis_id

Version 1では独自用語辞書・RAGを実装しない。

⸻

25. Risk

AIが案件上のリスク・注意事項を提案する。

severity:

low
medium
high

status:

open
resolved
dismissed

AIが生成したRiskは確定事実ではなく補助情報として扱う。

⸻

26. 矛盾

Version 1では専用Contradictionテーブルを必須としない。

AIが検出した矛盾は、

* RequirementChangeProposal
* Risk
* contradicting RequirementEvidence

などを利用して表現可能とする。

⸻

27. 再解析ルール

新しいConversation追加後、AI再解析を実行できる。

AIは以下を判断する。

* 新規RequirementSuggestion
* 既存Requirementへの追加Evidence
* RequirementIssue
* SuggestedQuestion
* Risk
* RequirementChangeProposal
* 矛盾

AIは既存Requirementを直接変更しない。

特にconfirmed要件の自動変更は禁止する。

⸻

28. 重複生成防止

Version 1では簡易的な重複判定を実装する。

候補:

* project_id
* category
* normalized title
* active status

完全一致・明らかな重複を無制限にINSERTしない。

EmbeddingやVector Searchによる高度な意味類似判定はVersion 1では実装しない。

⸻

29. AI解析排他制御

同一Projectで複数Analysisを同時実行しない。

サーバー側で、

Analysis.status = processing

が存在する場合、新規解析開始を拒否する。

想定:

HTTP 409 Conflict

画面には、

現在この案件を解析中です。
完了後に再度実行してください。

等を表示する。

フロントエンドのボタンdisabledだけに依存しない。

⸻

30. ユーザー単位レート制限

AI APIの無駄な連続実行を防止するため、ユーザー単位のRate Limitを設ける。

Version 1では例として、

1分間に5回

程度を初期値候補とする。

具体値は実装・利用状況に応じて調整可能とする。

Laravel RateLimiterを利用可能。

⸻

31. AIレスポンス

構造化レスポンスを使用する。

想定:

{
  "requirement_suggestions": [],
  "requirement_change_proposals": [],
  "requirement_issues": [],
  "suggested_questions": [],
  "terms": [],
  "risks": [],
  "contradictions": []
}

Schema Validationを必須とする。

⸻

32. AI解析ルール

AIへ最低限以下を指示する。

* 会話に存在しない事実を確定しない
* 明確な決定はsuggested_confirmedとして提案可能
* 推測はcandidate
* 不明確なものはunresolved
* 正式confirmedは人間のみが決定する
* confirmed要件を直接変更しない
* 変更可能性はRequirementChangeProposalにする
* Evidenceを可能な限り返す
* 根拠が複数あれば複数返す
* 既存要件を考慮する
* 矛盾を検出する
* 不明な場合は不明とする

⸻

33. RAG

Version 1では実装しない。

AIへ送信するのは原則として以下。

Project Context
+
Target Conversations
+
必要なContext Conversations
+
Current Requirements Snapshot
+
Fixed Prompt Rules

将来:

KnowledgeProvider
 ↓
RAG
 ↓
AnalysisInput

を追加可能とする。

⸻

34. セキュリティ

* API Keyをフロントへ露出しない
* API Keyは.env
* GitへAPI Keyをコミットしない
* ユーザーは自分のProjectのみ操作可能
* 会話全文を通常ログへ出さない
* raw_responseを通常ログへ出さない
* CSRF対策
* Validation
* Authorization
* Mass Assignment対策
* AI障害時に既存業務データを壊さない

⸻

35. 外部AI送信表示

AI解析前に以下の趣旨をユーザーへ表示する。

入力した会話内容は解析のため外部AIサービスへ送信されます。

初回確認を実装してもよい。

Version 1ではユーザー操作による明示的AI解析を基本とする。

⸻

36. API安全対策

最低限:

* 入力文字数上限
* API timeout
* 二重送信防止
* Project単位排他制御
* User単位Rate Limit
* processing表示
* エラー表示
* 再試行
* 不正レスポンス処理

⸻

37. テスト方針

テストを最後のPhaseだけに集中させない。

各Phaseで実装対象の最低限のUnit / Feature Testを同時に作成する。

Phase完了条件には、

実装 + 必要なテスト成功

を含める。

最終テストPhaseでは回帰テスト・統合テストを行う。

OpenAI APIは通常テストではMockする。

⸻

38. Git運用

基本:

main
develop
feature/*

機能単位でコミットする。

例:

feat: add conversation management
feat: add rule based conversation segmentation
feat: add analysis tracking
feat: add requirement suggestions
feat: add requirement evidence
feat: add change proposal approval flow

⸻

39. Codexへの指示

作業前に必ず確認する。

1. AGENTS.md
2. spec.md
3. TASKS.md
4. 現在の実装
5. git status
6. git diff

TASKS.mdのPhase単位で進める。

一度に複数Phaseを無断で実装しない。

### タスク進捗・作業ログ

作業開始時に、対象Phaseの`TASKS.md`の進捗を`[~]`へ更新する。

対象Phaseの実装・テスト・完了条件をすべて満たした場合のみ、進捗を`[x]`へ更新する。ブロックした場合は`[!]`へ更新する。

各Phaseの完了またはブロック時に、`worklog.md`へ以下を追記する。

* Phaseと進捗
* 実装内容・変更ファイル
* テスト結果
* ADRへのリンクまたは実装上の判断
* 未解決事項・次のアクション

`worklog.md`には、API Key、会話全文、raw_response、その他の機密情報・個人情報を記録しない。

仕様にない機能を先回りしない。

仕様と実装に矛盾がある場合は勝手に解釈せず報告する。

特に以下は禁止する。

* AIによるRequirement自動confirmed
* AIによるconfirmed Requirement自動更新
* AI responseの直接DB業務データ化
* Version 1へのRAG導入
* Version 1への音声機能導入
* Version 1へのオンライン会議連携導入

⸻

40. Version 1.2の最終原則

常に以下の境界を守る。

Input Source
 ↓
Conversation
 ↓
Analysis
 ↓
RequirementSuggestion
 ↓
Human Review
 ↓
Requirement

また、

Analysis Result
≠
Official Business Data

および、

Past Evidence
≠
Current State

を区別する。

Version 1.2の目的は、

会話からAIが要件定義を支援し、その提案・根拠・履歴を保持しながら、人間が安全に正式要件を育てられるシステムを完成させること。

である。
