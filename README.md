<p align="center">
  <img src="images/logo.png" alt="DSGVO Compliance Audit Logo" width="200" />
</p>

# DSGVO/GDPR Compliance Audit Skill

A read-only, multi-phase audit protocol for AI coding assistants (Claude Code, Antigravity, and compatible agents). Systematically scans a codebase for personal data exposure, missing legal bases, broken data subject rights endpoints, and third-party data processor gaps under German DSGVO/BDSG/TTDSG law.

Developed against a Turborepo monorepo stack (Next.js, Fastify, Electron, Prisma/PostgreSQL, Stripe, Twilio, RustDesk). Stack-specific sections are marked and can be adapted for other architectures.

## Phases

| Phase | Focus | DSGVO Anchor |
|-------|-------|--------------|
| 0 | PII Inventory — what data exists and where | Art. 4, 30 |
| 1 | Legal Basis & Consent | Art. 6, 7, 9 |
| 2 | Data Subject Rights endpoints | Art. 15–22 |
| 3 | Data Minimization & Retention | Art. 5(1)(c)(e) |
| 4 | Third-Party Processors & Transfers | Art. 28, 44ff |
| 5 | Technical Security Measures | Art. 32 |
| 6 | DPIA Threshold, Impressum, Datenschutzerklärung | Art. 13, 14, 35; §5 DDG |

## How to Use

1. **Install:** Save `SKILL.md` into `~/.claude/skills/dsgvo-audit/SKILL.md` (or the equivalent skills directory for your agent).
2. **Invoke:** `/dsgvo-audit` — runs the full 7-phase audit. Or with a scope: `/dsgvo-audit Scope: auth-registration`
3. **Multi-agent pipeline:** See [tasks-dsgvo-example.md](./tasks-dsgvo-example.md) for a 9-scope parallel audit task list (dual independent runs + orchestrator merge).

## Multi-Agent Pipeline

[tasks-dsgvo-example.md](./tasks-dsgvo-example.md) implements an 18-subagent parallel audit (9 scopes × 2 independent runs) with an orchestrator merge pass and challenge verification before the final report.

1. Launch all 18 run-A/run-B tasks in parallel.
2. Merge each scope pair yourself as they complete — no subagent for the merge step.
3. Challenge pass: re-verify all CRITICAL/HIGH findings against actual code before compiling the final report.
4. Compile `DSGVO_FINAL_r1.md` with Compliance Score, DPIA recommendation, and AVV checklist.

## Known Limitations

- Legal conclusions are based on DSGVO/BDSG/TTDSG as in force at time of writing. Verify currency before using findings in formal legal filings.
- RustDesk, Stripe, and Twilio sections reflect the reference stack — adapt processor names for other architectures.
- Procedural compliance gaps (missing DPA contracts, absent DPIA documentation) are flagged where code evidence is absent, but cannot be confirmed from source alone.
- Severity reflects regulatory fine risk (Art. 83 DSGVO), not exploitability. Pair with `forensic-audit` for security depth.
- The PII Quick Scan uses grep patterns and will produce false positives. The Challenge Pass exists to filter these before the final report.
