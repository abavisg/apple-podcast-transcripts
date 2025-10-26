# 🧪 claude-testing.md — Testing Philosophy & Practice

## 🔁 Core Philosophy
Test **behavior**, not internals. Follow the Testing Pyramid: **Unit → Integration → E2E**. Contracts prevent drift.

## ✅ Behavior-Driven Scenarios
Describe business outcomes in plain language.
```gherkin
Given a logged-in user
When they submit a valid form
Then they see a success message
```

## 🏗 Test Architecture
- `/tests/unit` — isolated, fast feedback
- `/tests/integration` — component/service contracts
- `/tests/e2e` — user journeys or API flows

## 📈 Coverage & Confidence
Target **meaningful** ~80% coverage. Track **test debt** explicitly. Prefer **mutation testing** to verify test quality where feasible.

## 🔬 Non-Functional Tests
- Performance budgets & regressions
- Security scans (SAST/DAST)
- Accessibility (a11y) checks
- Chaos/Resilience where relevant

## 🚫 Anti-Patterns
- Mocking everything
- Testing libraries not behavior
- Brittle selectors in UI tests
