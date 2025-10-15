# WebhookControl

Resilient webhook delivery platform with full observability and queue control. Built on Laravel 12, powered by Redis for asynchronous processing, and monitored via Laravel Horizon.

---

## Why this project?

Running webhooks reliably in production is hard. Intermittent failures, timeouts, incorrect backoffs, lack of logs, missing observability, and the difficulty of replaying events can break critical integrations. WebhookControl addresses these by providing:

- Reliable delivery with exponential retries and queue control.
- Complete request/response logging for auditing and troubleshooting.
- Real-time worker and queue monitoring (Horizon).
- Manual replay of failed events with full visibility.
- Signature validation (HMAC) to secure integrations.

---

## Features

- Webhook registration and per-endpoint configuration.
- Automatic retry with exponential backoff.
- Detailed request/response logging (payload, status, timing).
- Manual replay of failed webhooks.
- HMAC signature validation for integrity and authenticity.
- Queue-based execution (Redis) with queue/tag isolation.
- Queue/worker dashboard and metrics via Laravel Horizon.
- API-first design (JSON REST) for programmatic integration.
- Testing and simulation utilities for webhooks.

Note: An advanced custom UI dashboard is under active development; Horizon already provides robust operational visibility.

---

## Architecture & Components

- Application: Laravel 12 (PHP 8.2+).
- Queue: Redis (Predis client).
- Database: MySQL (default in Docker dev; PostgreSQL supported).
- Monitoring: Laravel Horizon (available at `/horizon`).
- Local environment: Docker Compose via Laravel Sail.
- Dev email: Mailpit (web UI on configured port).

---

## Key libraries

- `laravel/framework` ^12.0 – core framework.
- `laravel/horizon` ^5.36 – queue monitoring and management.
- `predis/predis` ^3.2 – Redis client (no native extension required).
- `laravel/tinker` ^2.10 – interactive shell.

Development/QA:
- `laravel/sail` – Dockerized environment.
- `laravel/pint` – code formatting.
- `nunomaduro/collision`, `phpunit/phpunit` – DX and testing.
- `laravel/pail` – development log streaming.

---

## How to run (Docker)

Prerequisites: Docker and Docker Compose.

1. Copy `.env` and configure variables:
   ```bash
   cp .env.example .env
   ```
2. Start services:
   ```bash
   docker compose up -d
   ```
3. Generate app key and run migrations:
   ```bash
   docker compose exec laravel.test bash -lc "php artisan key:generate --ansi"
   docker compose exec laravel.test bash -lc "php artisan migrate --force --ansi"
   ```
4. Start Horizon (optional but recommended):
   ```bash
   docker compose exec laravel.test bash -lc "php artisan horizon"
   ```

Default URLs (with `APP_PORT=8000` and `FORWARD_MAILPIT_DASHBOARD_PORT=8025`):
- App: `http://localhost:8000`
- Horizon: `http://localhost:8000/horizon`
- Mailpit: `http://localhost:8025`

> Note: Actual ports are controlled by variables in `compose.yaml`.

---

## Configuration (.env)

Minimal variables for the default Docker Compose setup:

```dotenv
APP_NAME=WebhookControl
APP_ENV=local
APP_URL=http://localhost
APP_PORT=8000

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=webhookcontrol
DB_USERNAME=sail
DB_PASSWORD=password

QUEUE_CONNECTION=redis
REDIS_CLIENT=predis
REDIS_HOST=redis
REDIS_PORT=6379
```

For Horizon, keep `QUEUE_CONNECTION=redis` and ensure Redis is reachable at host `redis` (Docker network). Access to the Horizon UI is controlled by the `viewHorizon` gate (see `App\Providers\HorizonServiceProvider`).

---

## Running without Docker (dev alternative)

With PHP 8.2+, local Redis and MySQL configured, you can use:

```bash
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate --force

# Dev: server, queue, logs and Vite in parallel
composer run dev
```

> On Windows, ensure your terminal supports parallel processes (via `npx concurrently`).

---

## Monitoring & Observability

- Horizon: metrics, worker status, queues, pending/completed/failed jobs.
- Logs: Laravel (Monolog) plus `laravel/pail` for dev streaming.
- Database: `failed_jobs` table to inspect failures and trigger replays.

---

## Security

- HMAC signature validation for webhooks (recommended: `X-Signature` with HMAC-SHA256 and per-integration secret).
- TLS/HTTPS required in production for external endpoints.
- Horizon access restricted outside `local` environment via gate (user/email whitelist).

---

## Troubleshooting (Windows)

- Line endings: avoid CRLF in files copied into containers; use LF. Common issues:
  - `bash\r: No such file or directory` when starting containers.
  - MySQL init scripts fail due to `\r`. Fix by enforcing LF or removing problematic init scripts.
- If needed, normalize files via `sed`/`dos2unix` or configure `git` with `autocrlf=false` for the repo.

---

## Roadmap

- First-party monitoring dashboard (beyond Horizon).
- Optional Python worker for advanced validations and custom pipelines.
- Integration templates and consumer examples.

---

## License

MIT – see `LICENSE`.

---

## Contributing

Issues and PRs are welcome. Feature requests, DX improvements, and integration examples will help the platform evolve.