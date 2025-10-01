# WebhookControl

**WebhookControl** is a Laravel-based platform that provides reliable webhook delivery with automatic retries, failure logging, and a complete monitoring dashboard. It is designed to help developers and companies improve the resilience and observability of their webhook infrastructure.

---

## 🚀 Features

- ✅ Webhook registration and configuration
- 🔄 Automatic retry with exponential backoff
- 🪵 Request/response logging with full payload history
- 🧠 Real-time monitoring dashboard (coming soon)
- 📤 Manual webhook replay
- 🔐 Webhook signature validation with HMAC
- ⌛ Queue-based execution (Redis-powered)
- 🧰 API-first design (JSON REST)
- 🧪 Webhook testing and simulation
- 🔧 Optional Python worker for advanced validation

---

## ⚙️ Tech Stack

- **Laravel** (PHP framework)
- **Redis** (queue and caching)
- **MySQL / PostgreSQL** (storage)
- **Laravel Horizon** (queue monitoring)
- **Python (optional)** – for advanced async payload processing

---

## 📦 Installation

```bash
git clone https://github.com/gabrielvalenco/webhookcontrol.git
cd webhookcontrol
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate
