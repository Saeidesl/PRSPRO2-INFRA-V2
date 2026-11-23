# Task 2.8 Increment 126 — Telegram pairing, polling and notification boundary

Date: 2026-11-23  
Evidence: E1 — read-only source inspection

## Source inspected
TelegramBotService.js and Code.js dispatch/configuration entrypoints.

## Observed behavior
- Bot credentials, webhook secret, pending registration state, update offset and tenant-to-chat mapping are stored in Script Properties, not source constants. The source documents secret requirements but live configuration was not inspected.
- Webhook path compares the query secret before processing message updates. The pairing flow is time-bounded (10-minute window), requires a private chat and checks the sole active tenant state before registering chat recipients.
- Polling setup removes the webhook, preserves pending updates, replaces prior polling triggers and creates a one-minute trigger. Polling reads updates using stored offset and advances offset after processing each update; durable queue/lease/dead-letter/recovery guarantees are not demonstrated.
- Commands support pairing/help/chat ID; chat mapping is JSON per tenant. Configuration validation prevents malformed mapping from being overwritten during pairing.
- Project notification is gated by project tenant and NotifyEnabled; event notifications require tenant context and project availability when linked. Recipient list and token must be configured.
- Notification payloads include project/event context, event variants, central information or legacy dynamic/form data, and attachment metadata/content. Content is split into bounded message chunks and attachments are sent as documents.
- Send operations return counts/statuses (SENT/PARTIAL/FAILED) but a durable retry queue, idempotent delivery, rate-limit strategy and notification audit ledger are not shown. Notification failures can be partial across recipients/chunks/attachments.
- Code.js routes Telegram webhook dispatch separately from regular PMS requests via telegramSecret parameter marker; setup/polling helper functions are present.

## V2 implications
1. Store secrets in managed secret storage; never persist bot tokens in repository or logs. Tenant/chat pairing must be admin-authorized, expiring, auditable and safely revocable.
2. Separate notification domain/outbox from request path; enqueue on event/project commits and process asynchronously with durable retry, idempotency, rate limits and dead-letter/reconciliation.
3. Define notification privacy/permission rules, recipient management, event opt-in, attachment access and data-minimization policies.
4. Use webhook signature/secret verification at ingress and avoid secret query-string exposure; polling is not assumed as target architecture.
5. Persist delivery status per recipient/channel and distinguish delivery attempts from successful delivery; avoid repeated duplicate messages on retries.
6. Test message formatting, truncation, sensitive-field filtering, multi-recipient partial failure, attachment permissions, retry and revocation using synthetic data only.

## Limits
- Source-only inspection; no live Telegram configuration, messages, credentials or chat IDs accessed; no runtime tests or production changes.
- E1 only; G-01/G-10 remain open; schema readiness NOT APPROVED.
