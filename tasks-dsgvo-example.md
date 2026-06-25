# DSGVO Audit Task List — r1
Output dir: C:\Users\relig\Desktop\_Reports\dsgvo-audit_r1\

## Execution Strategy (CRITICAL — read before starting)

### Фаза 1: Паралельний аудит (18 субагентів)
- Запусти ВСІ 18 runA/runB (01a-09b) **одним батчем паралельно**.
- Кожен субагент: читає свій скоуп → пише FINAL.md з DSGVO-знахідками.

### Фаза 2: Merge per scope (РОБИ САМ, без субагентів)
- **Не чекай усіх 18.** Як тільки runA + runB для одного скоупу обоє готові → одразу сам роби merge в MERGED.md.
- Merge логіка: 2 FINAL → 1 MERGED (вища severity, дедублікація, теги [Run A only]/[Run B only]).

### Фаза 3: Final cross-scope merge (РОБИ САМ)
- Коли всі 9 MERGED.md готові → сам читаєш усі → DSGVO_FINAL_r1.md.
- **Включи ВСІ знахідки.** CRITICAL + HIGH — кожен з file:line, статтею DSGVO і описом. MEDIUM + LOW — згруповані за скоупом.
- Крос-скоуп дедублікація: однаковий паттерн у кількох скоупах → один запис з переліком скоупів.
- Сортування: CRITICAL → HIGH → MEDIUM → LOW.
- Додай Compliance Score: 100 − (CRITICAL×20 + HIGH×10 + MEDIUM×5 + LOW×1).
- Додай: DPIA erforderlich? JA/NEIN/UNKLAR + обґрунтування.

### Чому без субагентів на merge/final
- Merge (2 інпути): ти робиш це швидше сам.
- Final (9 інпутів): крос-скоуп дедублікація потребує повного контексту — він є тільки в тебе.

---

## Rules

- Prompt для кожного runA/runB таску:
  "/dsgvo-audit. Scope: [назва скоупу]. App: [тег].
  Relevant files (read these first, then discover more via skill pre-flight if needed):
  [список файлів]
  READ-ONLY. Збережи FINAL у: [output path]
  Focus: знайди DSGVO/BDSG порушення та ризики тільки в цьому скоупі.
  Severity: CRITICAL=Art.83(5) fine risk / HIGH=likely violation / MEDIUM=gap / LOW=doc gap."

- Таски 01a-09b — два незалежні прогони того самого скоупу, кожен окремий субагент, без спільного контексту.

- Таски з суфіксом "merge" — НЕ делегуй субагенту. Зроби сам:
  прочитай обидва FINAL → зведи в MERGED.md
  (спільні знахідки: severity = вища з двох; унікальні: тег [Run A only]/[Run B only]).

- Останній таск (99) — НЕ делегуй субагенту. Зроби сам:
  читай усі MERGED.md → DSGVO_FINAL_r1.md.
  Включи: Compliance Score, DPIA-рішення, Bußgeldrisiko, Top-5 критичних, Quick Wins.

- Після кожного таску дописуй рядок у 00_PROGRESS.md:
  [NN] | [назва] | CRITICAL:X HIGH:X MEDIUM:X LOW:X | DONE

- **Context Preservation:** НЕ виводь повний вміст FINAL.md / MERGED.md у чат. Тільки підтверджуй шлях файлу та findings count, потім переходь до наступного таску. Це критично для збереження контексту оркестратора.

- **Directory Isolation:** Кожен скоуп — окрема папка. Не змішуй файли різних скоупів в одній директорії.
  Структура: `dsgvo-audit_r1/[NN]_[scope-name]/runA/`, `runB/`, `MERGED.md`.

- **Challenge Pass (перед таском 99):** Перед фінальним мержем, прочитай всі CRITICAL та HIGH знахідки з усіх MERGED.md. Для кожної перевір `file:line` в реальному коді ще раз і застосуй вердикт: **Confirmed / Overstated (downgrade) / Drop**. Запиши скільки було drop/downgrade — це маркер якості аудиту.

---

## Tasks

- [ ] 01a_prisma-schema | @imma-help/db | packages/db/prisma/schema.prisma, packages/db/prisma/migrations/ → dsgvo-audit_r1\01_prisma-schema\runA\
- [ ] 01b_prisma-schema | (same scope as 01a) → dsgvo-audit_r1\01_prisma-schema\runB\
- [ ] 01m_prisma-schema-merge | merge 01a + 01b → dsgvo-audit_r1\01_prisma-schema\MERGED.md
  Focus: PII fields, onDelete cascades, special category data (RustDesk rdId/rdPassword), retention fields, encryption markers.

- [ ] 02a_auth-registration | apps/web + apps/api | apps/web/src/app/(login|register|forgot-password|reset-password|onboarding), apps/api/src/routes/auth.ts → dsgvo-audit_r1\02_auth-registration\runA\
- [ ] 02b_auth-registration | (same scope as 02a) → dsgvo-audit_r1\02_auth-registration\runB\
- [ ] 02m_auth-registration-merge | merge 02a + 02b → dsgvo-audit_r1\02_auth-registration\MERGED.md
  Focus: Art.7 consent at signup, Art.13 info at collection, data minimization in registration form, PII in logs/responses.

- [ ] 03a_dsgvo-endpoints | apps/api | apps/api/src/routes/dsgvo.ts, apps/api/src/routes/account.ts (delete, export) → dsgvo-audit_r1\03_dsgvo-endpoints\runA\
- [ ] 03b_dsgvo-endpoints | (same scope as 03a) → dsgvo-audit_r1\03_dsgvo-endpoints\runB\
- [ ] 03m_dsgvo-endpoints-merge | merge 03a + 03b → dsgvo-audit_r1\03_dsgvo-endpoints\MERGED.md
  Focus: Art.15 export completeness (all PII fields?), Art.17 cascade delete (all models?), Art.20 machine-readable format, IDOR on export/delete, 1-month deadline tracking.

- [ ] 04a_rustdesk-consent | apps/desktop + apps/desktop-operator + apps/api | apps/desktop/src/rustdesk.ts, apps/desktop-operator/src, apps/api/src/routes/sessions.ts → dsgvo-audit_r1\04_rustdesk-consent\runA\
- [ ] 04b_rustdesk-consent | (same scope as 04a) → dsgvo-audit_r1\04_rustdesk-consent\runB\
- [ ] 04m_rustdesk-consent-merge | merge 04a + 04b → dsgvo-audit_r1\04_rustdesk-consent\MERGED.md
  Focus: Art.6/7 consent before remote session, consent logging (timestamp+IP+version), credential storage encryption, relay server location (EU/non-EU transfer?), Art.35 DPIA trigger (systematic monitoring of private computers).

- [ ] 05a_stripe-billing | apps/api + apps/web | apps/api/src/routes/(subscriptions|stripe|vouchers).ts, apps/web/src/app/(billing|subscription) → dsgvo-audit_r1\05_stripe-billing\runA\
- [ ] 05b_stripe-billing | (same scope as 05a) → dsgvo-audit_r1\05_stripe-billing\runB\
- [ ] 05m_stripe-billing-merge | merge 05a + 05b → dsgvo-audit_r1\05_stripe-billing\MERGED.md
  Focus: Art.28 AVV with Stripe, webhook signature verification, PII in Stripe event logs, EU data residency, international transfer basis (EU-US DPF adequacy decision).

- [ ] 06a_twilio-comms | apps/api | apps/api/src/routes/(notifications|reminders|sms).ts, twilio.ts (or similar) → dsgvo-audit_r1\06_twilio-comms\runA\
- [ ] 06b_twilio-comms | (same scope as 06a) → dsgvo-audit_r1\06_twilio-comms\runB\
- [ ] 06m_twilio-comms-merge | merge 06a + 06b → dsgvo-audit_r1\06_twilio-comms\MERGED.md
  Focus: Art.28 AVV with Twilio, EU region config (or US default?), PII in SMS content, phone number retention, opt-out/unsubscribe mechanism (Art.21).

- [ ] 07a_web-frontend | apps/web | apps/web/src/app/(impressum|datenschutz|privacy|cookie), apps/web/src/app/layout.tsx, apps/web/src/app/page.tsx → dsgvo-audit_r1\07_web-frontend\runA\
- [ ] 07b_web-frontend | (same scope as 07a) → dsgvo-audit_r1\07_web-frontend\runB\
- [ ] 07m_web-frontend-merge | merge 07a + 07b → dsgvo-audit_r1\07_web-frontend\MERGED.md
  Focus: §5 DDG Impressum completeness, Art.13 Datenschutzerklärung completeness (all processors named?), §25 TTDSG cookie consent banner, analytics scripts (Google Analytics, Plausible, etc.), CSP headers.

- [ ] 08a_sse-logs | apps/api | apps/api/src/routes/queue-sse.ts, apps/api/src/routes/queue.ts, apps/api/src/plugins/logger.ts (or fastify config) → dsgvo-audit_r1\08_sse-logs\runA\
- [ ] 08b_sse-logs | (same scope as 08a) → dsgvo-audit_r1\08_sse-logs\runB\
- [ ] 08m_sse-logs-merge | merge 08a + 08b → dsgvo-audit_r1\08_sse-logs\MERGED.md
  Focus: Art.32 PII leakage in SSE event payloads (client names in queue events?), console.log/fastify.log PII exposure (email, phone, names, tokens), log retention policy, pseudonymization of analytics events.

- [ ] 09a_cron-retention | apps/api | apps/api/src/cron/ (proactive-outreach.ts, index.ts, and all cron files) → dsgvo-audit_r1\09_cron-retention\runA\
- [ ] 09b_cron-retention | (same scope as 09a) → dsgvo-audit_r1\09_cron-retention\runB\
- [ ] 09m_cron-retention-merge | merge 09a + 09b → dsgvo-audit_r1\09_cron-retention\MERGED.md
  Focus: Art.5(1)(e) Speicherbegrenzung — is there ANY automatic deletion of old PII? Session logs, queue history, appointment records, chat logs — retention period enforced or unbounded? HGB 10-year exception for billing records.

- [ ] 99_final-merge | склей усі MERGED.md → dsgvo-audit_r1\DSGVO_FINAL_r1.md
  Must include: Compliance Score (0-100), DPIA erforderlich (JA/NEIN/UNKLAR), Bußgeldrisiko (LOW/MEDIUM/HIGH), Top-5 CRITICAL/HIGH findings, Quick Wins (<1 day), Structural fixes (>1 day), AVV checklist (Stripe/Twilio/Hetzner/RustDesk — status per processor).
