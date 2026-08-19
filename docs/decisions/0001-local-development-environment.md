# ADR 0001: ローカル開発環境に最小Docker Compose構成を採用する

- Status: Accepted
- Date: 2026-08-18

## Context

Version 1ではLaravel、Docker Compose、MySQLを使う。開発環境は、初期構築が再現可能であり、アプリケーションとDBの起動・テストをコンテナ内で実行できる必要がある。

## Decision

`compose.yaml`でPHP 8.5アプリケーションコンテナとMySQL 8.4コンテナを構成する。アプリケーションコンテナは`php artisan serve`でポート8000を公開し、MySQL接続用の`pdo_mysql`とテスト用の`pdo_sqlite`を含める。

## Consequences

- 開発開始は`docker compose up -d --build`で行う。
- migrationとテストは`docker compose exec app php artisan ...`で実行する。
- 本構成はローカル開発用であり、本番デプロイ構成を規定しない。
