<p align="center">
  <img src="images/logo.png" alt="DSGVO Compliance Audit Logo" width="200" />
</p>

# ⚖️ DSGVO/GDPR Compliance Audit Skill

> **Professional Standard:** This framework has been validated through production audit runs on a Turborepo monorepo (Next.js, Fastify, Electron, Prisma/PostgreSQL). It represents a mature, systematic approach to automating compliance, data protection, and GDPR auditing, and can be adopted as a standard auditing pipeline for medium-to-large scale commercial projects.

> [!NOTE]
> This is a powered-up skill featuring a robust dual-run task system. The core execution pattern and task orchestration design are inspired by and adapted from the workflow of a talented GitHub user (whose username we unfortunately cannot recall, but to whom we express our sincere gratitude!). Thank you for sharing such a solid foundation!

A read-only, multi-phase audit protocol designed for AI coding assistants (such as Antigravity, Claude, or other agentic frameworks). This skill guides the agent to systematically search the codebase for personal data exposure (PII), missing legal bases under GDPR, broken data subject rights endpoints (export/deletion), and third-party data processor compliance gaps.

## 🌟 What It Does

The skill defines a structured, sequential auditing protocol focusing on:

* **Phase 0: Flow Map & DSGVO Scope** - Identifies all personal data collection points, user roles, databases, SSE queues, and third-party processors.
* **Phase 1: Database & Schema Audit** - Scans Prisma schema and database migrations for raw PII, onDelete cascade safety, encryption, and retention properties.
* **Phase 2: Authentication & Registration** - Verifies active consent logging (IP, timestamp, version), Art. 13 privacy notice presentation, and data minimization.
* **Phase 3: Data Subject Rights (Art. 15 / Art. 17)** - Audits IDOR-safe account export (machine-readable format) and full cascading deletion endpoints.
* **Phase 4: Remote Control & Screen Sharing** - Evaluates RustDesk or other remote access sessions for active consent, credential encryption, and DPIA necessity.
* **Phase 5: Payments & Billing** - Audits Stripe payment integrations, Art. 28 AVV agreements, webhooks, and EU residency compliance.
* **Phase 6: Communication Services** - Inspects Twilio integration, SMS/voice data retention, and unsubscribe options.
* **Phase 7: Frontend & Compliance Docs** - Verifies cookie banner TTDSG/DDG compliance, Google Fonts/Analytics localization, and Impressum completeness.
* **Phase 8: Log & Event Stream Safety** - Scans SSE event payloads and application logs to prevent PII leaks.
* **Phase 9: Retention & Cron Jobs** - Audits automatic deletion of expired PII against legal retention requirements (e.g. HGB 10-year exception).

---

## How to Use

1. **Install:** Save `SKILL.md` into `~/.claude/skills/dsgvo-audit/SKILL.md` (or your agent's config directory).
2. **Invoke:** Call it for a specific module or scope — e.g., `/dsgvo-audit. Scope: auth-registration.`
3. **Execution Plan:** See the reference task list in [tasks-dsgvo-example.md](./tasks-dsgvo-example.md).

---

## 📋 Orchestrating a Large-Scale DSGVO Audit (Multi-Agent Pipeline)

To ensure the highest audit depth, we use a structured **Multi-Agent Audit Pipeline**. The template is provided in [tasks-dsgvo-example.md](./tasks-dsgvo-example.md).

### How to use this strategy:
1. **Parallel Run A / Run B Execution:** Run two independent agent sessions concurrently for each scope (e.g. `01a_prisma-schema` and `01b_prisma-schema`) to avoid confirmation bias.
2. **Orchestrator Merge:** Automatically merge the findings, taking the highest severity and listing run-specific items.
3. **Challenge Pass:** Before compiling the final report, manually/programmatically verify all critical/high findings against the codebase to filter out false positives.
4. **Compile Final Report:** Generate the master compliance report containing a calculated Compliance Score and DPIA requirement recommendation.
