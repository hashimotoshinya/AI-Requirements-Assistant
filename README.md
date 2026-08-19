# AI Requirements Assistant

開発案件の会話をAIで解析し、要件候補・未確認事項・質問候補・リスクを整理して、人間が正式な要件へ育てるLaravelアプリケーションです。

仕様と開発ルールは[AGENTS.md](AGENTS.md)、[spec.md](spec.md)、[TASKS.md](TASKS.md)を参照してください。

## Development setup

必要なものはDocker DesktopとDocker Composeです。

```bash
cp .env.example .env
docker compose up -d --build
docker compose exec app php artisan migrate
```

アプリケーションは <http://localhost:8000> で利用できます。

停止する場合は次を実行します。

```bash
docker compose down
```

## Tests

```bash
docker compose exec app php artisan test
```

## Project records

- 実装進捗: [TASKS.md](TASKS.md)
- 作業ログ: [worklog.md](worklog.md)
- 実装上の判断: `docs/decisions/` のADR
