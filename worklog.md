# Work Log

実装Phaseごとの進捗、実装内容、検証結果、実装上の判断を記録する。

`TASKS.md`の進捗と整合させる。API Key、会話全文、raw_response、個人情報は記録しない。

## 記録ルール

- Phase開始時は`TASKS.md`を`[~]`に更新する。
- Phaseの実装・テスト・完了条件を満たした時点で`[x]`に更新し、ここへ完了記録を追記する。
- 作業が止まった場合は`[!]`に更新し、理由と次のアクションを記録する。
- 仕様変更を伴わない実装判断は、必要に応じて`docs/decisions/`のADRへ記録してリンクする。

## Entry template

### YYYY-MM-DD — Phase N: Phase name

- Status: `[x]` / `[!]`
- Summary:
- Changed files:
- Tests:
- Decisions / ADR:
- Open items / next action:
- Commit:

### 2026-08-18 — Phase 0: プロジェクト準備

- Status: `[!]`
- Summary: Gitリポジトリを初期化し、Laravel 13.25.0、Docker Compose上のPHP 8.5・MySQL 8.4開発環境、MySQL接続設定、READMEを整備した。
- Changed files: Laravelスケルトン一式、`compose.yaml`、`docker/php/Dockerfile`、`.dockerignore`、`.env.example`、`README.md`、`docs/decisions/0001-local-development-environment.md`。
- Tests: Docker Compose起動、MySQL migration、`php artisan test`（2 passed）、`http://127.0.0.1:8000/`（HTTP 200）。
- Decisions / ADR: [ADR 0001](docs/decisions/0001-local-development-environment.md)
- Open items / next action: GitHub CLIの認証後にGitHubリポジトリを作成し、初期コミットをpushする。
- Commit: 初期コミット作成予定
