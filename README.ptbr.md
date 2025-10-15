# WebhookControl

Plataforma de entrega de webhooks com resiliência, observabilidade e controle total de filas. Construída em Laravel 12, com processamento assíncrono em Redis e monitoramento via Laravel Horizon.

---

## Por que este projeto é necessário?

Integrar e consumir webhooks em produção é desafiador. Falhas intermitentes, timeouts, backoffs incorretos, falta de logs, ausência de observabilidade e dificuldade em reprocessar eventos podem comprometer integrações críticas. O WebhookControl resolve esses pontos com:

- Entrega confiável com retentativas exponenciais e controle de filas.
- Registro completo das requisições/respostas para auditoria e troubleshooting.
- Monitoramento de workers e filas em tempo real (Horizon).
- Reprocessamento manual de eventos (replay) e visibilidade de falhas.
- Validação de assinaturas (HMAC) para segurança de integrações.

---

## Funcionalidades

- Cadastro e configuração de webhooks por endpoint/serviço.
- Retentativa automática com backoff exponencial.
- Registro detalhado de requisições e respostas (payload, status, tempo).
- Reprocessamento manual de webhooks falhados (replay).
- Validação de assinaturas (HMAC) para garantir integridade/autenticidade.
- Execução baseada em filas (Redis) com isolamento por filas/etiquetas.
- Dashboard de filas e workers com métricas via Laravel Horizon.
- Design API-first (JSON REST) para integração programática.
- Ferramentas de teste e simulação de webhooks.

Nota: alguns módulos de UI avançada (dashboard próprio) estão em evolução; Horizon já fornece visibilidade operacional completa das filas.

---

## Arquitetura e Componentes

- Aplicação: Laravel 12 (PHP 8.2+).
- Fila: Redis (conexão via Predis).
- Banco de dados: MySQL (padrão em desenvolvimento com Docker; pode usar PostgreSQL).
- Monitoramento: Laravel Horizon (rotas próprias em `/horizon`).
- Ambiente local: Docker Compose com Laravel Sail.
- Email de desenvolvimento: Mailpit (UI acessível via porta configurada).

---

## Bibliotecas e dependências principais

- `laravel/framework` ^12.0 – framework principal.
- `laravel/horizon` ^5.36 – monitoramento e gestão de filas.
- `predis/predis` ^3.2 – cliente Redis (sem extensão nativa).
- `laravel/tinker` ^2.10 – execução interativa.

Desenvolvimento/QA:
- `laravel/sail` – ambiente Docker.
- `laravel/pint` – formatação de código.
- `nunomaduro/collision`, `phpunit/phpunit` – DX e testes.
- `laravel/pail` – streaming de logs.

---

## Como executar (Docker)

Pré‑requisitos: Docker e Docker Compose.

1. Copie o `.env` e configure as variáveis:
   ```bash
   cp .env.example .env
   ```
2. Suba os serviços:
   ```bash
   docker compose up -d
   ```
3. Gere a chave da aplicação e rode as migrações:
   ```bash
   docker compose exec laravel.test bash -lc "php artisan key:generate --ansi"
   docker compose exec laravel.test bash -lc "php artisan migrate --force --ansi"
   ```
4. Inicie o Horizon (opcional, mas recomendado):
   ```bash
   docker compose exec laravel.test bash -lc "php artisan horizon"
   ```

URLs padrão (com `APP_PORT=8000` e `FORWARD_MAILPIT_DASHBOARD_PORT=8025`):
- Aplicação: `http://localhost:8000`
- Horizon: `http://localhost:8000/horizon`
- Mailpit (dashboard de emails): `http://localhost:8025`

> Observação: as portas reais são definidas por variáveis em `compose.yaml`.

---

## Configuração (.env)

Variáveis mínimas para Docker Compose padrão:

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

Para Horizon, mantenha `QUEUE_CONNECTION=redis` e certifique‑se de que o Redis está acessível no host `redis` (rede Docker). A rota do Horizon é protegida pelo gate `viewHorizon` (veja `App\Providers\HorizonServiceProvider`).

---

## Execução sem Docker (alternativa de desenvolvimento)

Com PHP 8.2+, Redis local e MySQL configurados, é possível usar os scripts:

```bash
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate --force

# Dev: servidor, fila, logs e Vite juntos
composer run dev
```

> Em ambientes Windows, garanta que o terminal suporte os processos paralelos (via `npx concurrently`).

---

## Monitoramento e Observabilidade

- Horizon: métricas, status de workers, filas, jobs pendentes/concluídos/falhos.
- Logs: via Laravel (Monolog) e `laravel/pail` para streaming em desenvolvimento.
- Banco: tabela `failed_jobs` para inspecionar falhas e reprocessar.

---

## Segurança

- Validação de assinaturas HMAC em webhooks (modelo recomendado: `X-Signature` com HMAC‑SHA256 e segredo por integração).
- TLS/HTTPS obrigatório em produção para endpoints externos.
- Proteção do Horizon fora de ambiente `local` via gate (whitelist de emails/usuários).

---

## Troubleshooting (Windows)

- Line endings: evite CRLF em scripts/copias para containers; use LF. Problemas comuns:
  - `bash\r: No such file or directory` ao iniciar containers.
  - Scripts de init do MySQL com `\r` falham. Solução: garantir LF ou remover init problemático.
- Caso necessário, normalize arquivos com `sed`/`dos2unix` ou configure `git` para `autocrlf=false` no repositório.

---

## Roadmap

- Dashboard próprio de monitoramento (além do Horizon).
- Worker opcional em Python para validações avançadas e pipelines customizados.
- Templates de integração e exemplos de consumidores.

---

## Licença

MIT – veja `LICENSE`.

---

## Contato e Contribuição

Issues e PRs são bem‑vindos. Sugestões de funcionalidades, melhorias de DX e exemplos de integração ajudarão a evoluir a plataforma.