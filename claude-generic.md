# ⚙️ claude-generic.md — Engineering & Development Discipline

## 🧭 Core Principles
- **Clarity over cleverness**
- **Consistency over variety**
- **Maintainability over novelty**

## 🧱 Architecture Practices
- Modular, API-first design; stable contracts at boundaries.
- Isolate UI, domain, and infra layers.
- Prefer composition over inheritance.
- Document interfaces in `/docs` with examples.

## 🔄 Development Discipline
1. Small PRs with one purpose.
2. Descriptive commits (Conventional Commits).
3. Run tests + linters locally; keep CI green.
4. Avoid premature optimization and speculative abstractions.

## 💬 Pull Requests
- Title: `feat:`, `fix:`, `chore:`, etc.
- Include context, risks, screenshots/logs when relevant.
- Reference issues/ADRs and note any follow-ups.

## 🔒 Security & Privacy (essentials)
- Least privilege for tokens/keys; rotate regularly.
- No secrets in code; use vaults/CI secrets.
- Validate inputs at boundaries; sanitize outputs.
- Log PII-aware and redact sensitive data.

## 📊 Observability
- Minimum: structured logs + health checks.
- Preferred: metrics (RED/USE) + tracing.
- Define SLOs and error budgets when user-facing.

## 🚫 Anti-Patterns
- “Magic” frameworks without clear ROI.
- Hidden coupling through global state.
- Tests that mirror implementation details.
