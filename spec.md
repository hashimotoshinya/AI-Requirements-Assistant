spec.md

AI Requirements Assistant

Version 1.2 MVP 要件定義書

⸻

1. 概要

開発案件における会話をAIで解析し、要件定義を支援するWebアプリケーションを開発する。

Version 1ではテキストによる会話入力を対象とする。

ユーザーは、

* 1発言ずつ直接入力
* 複数話者を含む会話ログを一括貼り付け

できる。

AIは会話を解析し、

* RequirementSuggestion
* RequirementIssue
* SuggestedQuestion
* RequirementChangeProposal
* Term
* Risk
* 矛盾

等を生成する。

AI提案は正式要件とは分離し、正式Requirementの確定・変更・却下はユーザーが行う。

⸻

2. コンセプト

会話から要件を育てるAIアシスタント

単なるAIチャットではなく、

Conversation
 ↓
Analysis
 ↓
AI Suggestions
 ↓
Human Review
 ↓
Official Requirements

という業務フローを提供する。

⸻

3. Versionロードマップ

Version 1

テキスト入力・貼り付け。

Version 2

チャット形式による逐次入力。

Version 3

音声入力・文字起こし。

Version 4

Zoom / Google Meet / Teams等との連携。

全VersionでConversation以降の解析処理を可能な限り共通化する。

⸻

4. 技術構成

Backend:

* Laravel

Frontend:

* Blade
* Alpine.js必要時のみ

Database:

* MySQL

AI:

* OpenAI API

Development:

* Docker Compose
* Git
* GitHub

⸻

5. ユーザー認証

機能:

* Register
* Login
* Logout

ユーザーは自分のProjectのみアクセス可能。

Project Policy等によりAuthorizationを実装する。

⸻

6. Project

projects

id
user_id
name
client_name
description
memo
created_at
updated_at

機能:

* 一覧
* 作成
* 詳細
* 編集
* 削除

⸻

7. Conversation

Conversationは原則1話者・1発言。

conversations

id
project_id
speaker
content
source_type
source_reference
spoken_at
created_at
updated_at
deleted_at

speaker:

customer
developer
other
unknown

source_type:

manual
paste
chat
audio
zoom
google_meet
teams

Version 1使用:

manual
paste

Laravel SoftDeletesを利用する。

⸻

8. Conversation直接入力

入力:

* speaker
* content

1発言単位で保存する。

⸻

9. Conversation一括貼り付け

複数話者を含む会話ログを貼り付ける。

例:

顧客：
予約システムを作りたいです。
開発者：
ユーザー登録は必要ですか？
顧客：
はい。メールアドレスとパスワードでお願いします。

Version 1ではルールベースで分割する。

処理:

Raw Paste
 ↓
RuleBasedConversationSegmenter
 ↓
Segments
 ↓
Preview
 ↓
User Edit
 ↓
Save

自動分割を無確認で正式保存しない。

分割できない場合は単一Conversationとして保存可能。

⸻

10. ConversationRevision

Conversation編集履歴を保持する。

conversation_revisions

id
conversation_id
changed_by_user_id
before_speaker
after_speaker
before_content
after_content
created_at

RequirementEvidenceは解析時点のquoted_textを保持する。

⸻

11. Analysis

AI解析実行単位。

analyses

id
project_id
model
prompt_version
status
raw_response
parsed_response
current_requirements_snapshot
error_message
started_at
completed_at
created_at
updated_at

status:

processing
success
failed

⸻

12. AnalysisConversation

Analysisが利用したConversationを追跡する。

analysis_conversations

id
analysis_id
conversation_id
context_type
created_at

context_type:

target
context

target:
今回の主要解析対象。

context:
文脈維持目的の過去Conversation。

⸻

13. Analysis入力

AIへ最低限以下を送信する。

* Project概要
* target Conversation
* 必要なcontext Conversation
* Current Requirements snapshot
* 固定解析ルール
* Prompt version

Version 1ではRAGを使用しない。

⸻

14. RequirementSuggestion

AIが生成した要件提案を保存する。

requirement_suggestions

id
project_id
analysis_id
category
title
description
ai_judgement
confidence
status
created_at
updated_at

ai_judgement:

suggested_confirmed
candidate
unresolved

status:

pending
accepted
rejected

意味:

suggested_confirmed

会話上かなり明確に決定しているとAIが判断した状態。

candidate

候補ではあるが確定ではない。

unresolved

曖昧または追加確認が必要。

これらはすべてAI判断であり、正式Requirement.statusとは別である。

⸻

15. RequirementSuggestion承認

RequirementSuggestionをユーザーが確認する。

操作候補:

* 確定として採用
* candidateとして採用
* 編集して採用
* 却下

例:

AI Suggestion
メール＋パスワードログイン
AI判断:
suggested_confirmed
Confidence:
0.98
Evidence:
「メールアドレスとパスワードでお願いします」
[確定として採用]
[候補として採用]
[編集]
[却下]

⸻

16. Requirement

正式な要件。

requirements

id
project_id
category
title
description
status
created_by
confirmed_by
confirmed_at
created_at
updated_at

status:

candidate
confirmed
rejected

created_by:

ai_suggestion
user

AIはRequirementを直接confirmedにしない。

⸻

17. RequirementEvidence

requirement_evidences

id
requirement_id
conversation_id
analysis_id
quoted_text
evidence_type
created_at

evidence_type:

direct
supporting
contradicting

Requirementは複数Evidenceを保持可能。

⸻

18. RequirementRevision

requirement_revisions

id
requirement_id
changed_by_user_id
change_type
before_data
after_data
created_at

change_type例:

created
updated
confirmed
rejected
change_proposal_applied

before_data / after_dataはJSON保存可能。

⸻

19. RequirementIssue

要件定義上の未確認事項。

requirement_issues

id
project_id
analysis_id
category
title
description
reason
priority
status
created_at
updated_at

status:

open
resolved
dismissed

例:

予約キャンセル仕様が未決定

⸻

20. SuggestedQuestion

RequirementIssueを解消するための質問。

suggested_questions

id
project_id
analysis_id
requirement_issue_id
question
reason
priority
status
created_at
updated_at

status:

pending
asked
resolved
dismissed

例:

Issue:
予約キャンセル仕様が未決定
Question:
「ユーザー自身で予約をキャンセルできますか？」

⸻

21. RequirementChangeProposal

AIが既存Requirementの変更可能性を検出した場合に作成する。

requirement_change_proposals

id
project_id
requirement_id
analysis_id
proposed_category
proposed_title
proposed_description
reason
confidence
status
created_at
updated_at

status:

pending
accepted
rejected

既存RequirementをAIが直接変更しない。

⸻

22. ChangeProposal適用

accepted時:

1. 現Requirementを取得
2. RequirementRevisionへbefore_data保存
3. Requirementを更新
4. Revisionへafter_data保存
5. Evidenceを追加
6. Proposalをacceptedへ更新

confirmed Requirementも同様に人間の承認が必要。

⸻

23. Term

terms

id
project_id
analysis_id
term
description
context_description
created_at
updated_at

例:

OAuth
JWT
Stripe
Webhook
REST API

Version 1では独自辞書・RAGなし。

⸻

24. Risk

risks

id
project_id
analysis_id
title
description
severity
status
created_at
updated_at

severity:

low
medium
high

status:

open
resolved
dismissed

⸻

25. 矛盾

Version 1では専用テーブルを必須としない。

以下のいずれかで管理する。

* RequirementChangeProposal
* Risk
* contradicting Evidence

例:

既存Requirement:
Stripeを使用する
新Conversation:
「PayPalに変更したい」
↓
ChangeProposal
+
contradicting Evidence

⸻

26. Feedback

feedback

id
user_id
project_id
target_type
target_id
rating
comment
created_at
updated_at

rating:

useful
unnecessary
corrected

Version 1ではFeedbackによる自動学習を行わない。

⸻

27. AI構造化レスポンス

例:

{
  "requirement_suggestions": [
    {
      "category": "authentication",
      "title": "メールログイン",
      "description": "メールアドレスとパスワードでログインする",
      "ai_judgement": "suggested_confirmed",
      "confidence": 0.98,
      "evidences": [
        {
          "conversation_id": 12,
          "quote": "メールアドレスとパスワードでお願いします"
        }
      ]
    }
  ],
  "requirement_change_proposals": [],
  "requirement_issues": [],
  "suggested_questions": [],
  "terms": [],
  "risks": [],
  "contradictions": []
}

⸻

28. Schema Validation

以下を検証する。

* JSON形式
* 必須キー
* データ型
* Enum値
* Confidence範囲
* 最大件数
* 最大文字数
* Conversation IDの妥当性

不正レスポンスは正式業務データへ反映しない。

Analysisをfailedにして再試行可能にする。

⸻

29. raw_response

保存可能。

用途:

* デバッグ
* 解析再現
* Prompt改善
* 不具合調査

ただしRequirement等の正式データとして使用しない。

通常画面の正本にも使用しない。

⸻

30. Current Requirements Snapshot

Analysis時点のRequirementsをJSONで保存する。

例:

[
  {
    "id": 1,
    "category": "payment",
    "title": "Stripe決済",
    "status": "confirmed"
  }
]

これにより再解析時点の状態を追跡可能にする。

⸻

31. AI再解析

新しいConversation追加後に再解析できる。

AIは以下を生成可能。

* 新RequirementSuggestion
* 追加Evidence
* RequirementIssue
* SuggestedQuestion
* Risk
* ChangeProposal
* Contradiction

既存Requirementは自動更新しない。

⸻

32. Analysis排他制御

同一Projectで、

status = processing

のAnalysisが存在する場合、新規Analysis開始を拒否する。

想定レスポンス:

409 Conflict

⸻

33. Rate Limit

ユーザー単位でAI解析回数を制限する。

初期候補:

5 requests / minute

具体値は設定値として変更可能にする。

⸻

34. 入力上限

会話入力・解析対象には文字数上限を設定する。

具体値は使用モデルや実装時のコストを確認して設定する。

上限超過時は、

* エラー表示
* 会話分割を促す

⸻

35. Timeout

OpenAI API呼び出しにはTimeoutを設定する。

Timeout発生時:

* Analysis = failed
* error_message保存
* 既存正式データ変更なし
* 再試行可能

⸻

36. 二重送信

フロント:

* ボタンdisable
* Loading表示

サーバー:

* processing Analysis確認
* Rate Limit

双方を実装する。

⸻

37. AI外部送信表示

解析ボタン付近に表示する。

入力した会話内容は解析のため外部AIサービスへ送信されます。

必要に応じて初回同意を保存する。

⸻

38. UI

案件詳細画面の基本構成:

┌──────────────────────────────────────┐
│ Project                              │
├───────────────────┬──────────────────┤
│ Conversation      │ AI Assistant     │
│                   │                  │
│ 顧客: ...         │ Issues           │
│ 自分: ...         │ Questions        │
│                   │ Terms            │
│ [会話追加]        │ Risks            │
│ [まとめて貼付]    │                  │
│ [AI解析]          │                  │
├───────────────────┴──────────────────┤
│ Requirement Suggestions              │
│                                      │
│ AI: suggested_confirmed              │
│ [確定] [候補] [編集] [却下]         │
├──────────────────────────────────────┤
│ Official Requirements                │
│                                      │
│ ✓ Authentication                     │
│ ✓ Reservation                        │
├──────────────────────────────────────┤
│ Change Proposals                     │
└──────────────────────────────────────┘

⸻

39. RAG

Version 1では使用しない。

将来、

KnowledgeProvider
 ↓
Knowledge Search
 ↓
AnalysisInput

を追加可能とする。

⸻

40. セキュリティ

* .envでAPI Key管理
* Gitへ秘密情報を含めない
* Authorization
* Validation
* CSRF
* Mass Assignment
* Soft Delete
* 会話全文を通常ログへ出さない
* raw_responseを通常ログへ出さない
* 他ユーザーProjectアクセス禁止

⸻

41. Version 1完成条件

以下の一連処理が成立する。

Register
 ↓
Login
 ↓
Project作成
 ↓
Conversation入力
または
会話一括貼り付け
 ↓
Rule Based分割
 ↓
Preview / 修正
 ↓
保存
 ↓
AI Analysis
 ↓
RequirementSuggestions
 ↓
Issues / Questions / Terms / Risks
 ↓
Human Review
 ↓
Official Requirements
 ↓
追加Conversation
 ↓
Re-analysis
 ↓
ChangeProposal
 ↓
Human Review

⸻

42. Version 1.2中心原則

以下を必ず区別する。

AI Suggestion
≠
Official Requirement
Current Requirement
≠
Historical Evidence
AI Analysis Result
≠
Human Decision

Version 1.2ではこの3つの境界を崩さない。