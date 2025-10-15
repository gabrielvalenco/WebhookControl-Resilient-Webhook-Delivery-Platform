# Changelog

All notable changes to this project are documented in this file.

The format follows Keep a Changelog and adheres to Semantic Versioning.

## [0.1.0] - 2025-10-15

Initial release of WebhookControl – Resilient Webhook Delivery Platform.

### Added
- Resilient webhook delivery flow using Laravel 12, queues, and retries.
- Redis-backed queue with Laravel Horizon for monitoring at `/horizon`.
- Docker Compose environment: `laravel.test`, `mysql`, `redis`, `mailpit`.
- Portuguese and English READMEs with setup, architecture, and security guidance.
- Providers registration to enable Horizon and app services.
- Basic favicon served from `public/favicon.ico`.

### Fixed
- Resolved `404` for `/horizon` by registering service providers.
- Resolved `404` for `favicon.ico`; clarified empty icon and provided guidance.

### Security
- Verified no secrets in VCS; `.env` is ignored and not tracked.
- Recommendations for production hardening and CI secret management documented.

### Notes
- `compose.yaml` is intended for development. In production, do not expose DB/Redis/Mailpit ports and use strong credentials.

---

For detailed documentation, see `README.md`, `README.ptbr.md`, and `README.en.md`.

[0.1.0]: https://github.com/gabrielvalenco/WebhookControl-Resilient-Webhook-Delivery-Platform/releases/tag/v0.1.0