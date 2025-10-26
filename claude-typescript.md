# 💻 claude-typescript.md — TypeScript Standards

## 🧱 Schema-First Development
Define Zod (or similar) schemas first. Derive types from schemas, validate at boundaries.

## 🧪 Testing Stack
- Unit: Vitest / Jest
- Integration: Supertest (APIs), Testing Library (components)
- E2E: Playwright / Cypress

## ✍️ Code Style
- `strict` types; avoid `any`
- ESLint + Prettier; import/order rules
- Named exports; avoid default unless ergonomically required

## 🧩 Patterns
- Functional core, imperative shell
- React: container/presenter split where helpful
- API clients generated from OpenAPI when possible
