# doc-map.md Template

`doc-map.md` is the documentation index. Every file and folder inside `/docs` must appear here. It exists so any agent or developer can locate information in one look — without browsing the folder tree.

**Rules:**
- Update every time a file or folder is added, renamed, or removed from `/docs`.
- Never include `/planning-specifications` — those are not project docs.
- Keep entries short: one line per file, describing what it contains.
- Links must be relative paths from the `doc-map.md` file location.

---

## Template

```markdown
*Last Updated: YYYY-MM-DD*
---
# 🗺️ Project Documentation Map

This map is designed to help you find the right information quickly.

## 📌 Quick Access
- **Getting Started:** [README.md](../../README.md) — Setup & installation.
- **Development Workflow:** [standard-work-procedures.md](./standard-work-procedures.md)
- **Change History:** [changelog.md](./changelog.md)
- **Architecture Overview:** [architecture-overview.md](./architecture/architecture-overview.md)

## 📂 Directory Structure

### 🏗️ Architecture & Decisions
- [Architecture Overview](./architecture/architecture-overview.md) — System-wide high-level design.
- [Architecture Decision Records (ADR)](./architecture/architecture-decision-record.md) — History of "Why" we chose specific technologies.

### 🧩 Feature Specifications
Detailed rules, diagrams, and tests for each module:
- [Feature 1](./features/01-feature1/feature1-spec.md) — One-line description.
- [Feature 2](./features/02-feature2/feature2-spec.md) — One-line description.

### 🛠️ DevOps & Infrastructure
- [Infrastructure Map](./devops/infrastructure-map.md) — Cloud and network layout.
- [Environment Variables](./devops/env-variables.md) — Configuration guide.
- [Disaster Recovery](./devops/runbooks-disaster.md) — Critical incident procedures.

### 📐 Standards
- [Coding Standards](./standards/coding-standards.md)
- [Git Flow](./standards/git-flow.md)

### 🧪 QA & Testing
- [Test Plan](./qa-testing/test-plan.md) — Overall test strategy.
- [Acceptance Criteria](./qa-testing/acceptance-criteria.md)

### 🔌 API Reference
- [API List](./api/api-list.md) — All endpoints, methods, and contracts.
```

---

## Worked Example

```markdown
*Last Updated: 2026-04-13*
---
# 🗺️ Project Documentation Map

This map is designed to help you find the right information quickly.

## 📌 Quick Access
- **Getting Started:** [README.md](../../README.md) — Setup & installation.
- **Development Workflow:** [standard-work-procedures.md](./standard-work-procedures.md)
- **Change History:** [changelog.md](./changelog.md)
- **Architecture Overview:** [architecture-overview.md](./architecture/architecture-overview.md)

## 📂 Directory Structure

### 🏗️ Architecture & Decisions
- [Architecture Overview](./architecture/architecture-overview.md) — System-wide high-level design.
- [Architecture Decision Records (ADR)](./architecture/architecture-decision-record.md) — History of "Why" we chose specific technologies.

### 🧩 Feature Specifications
Detailed rules, diagrams, and tests for each module:
- [Authentication](./features/01-auth/auth-spec.md) — JWT-based login, token refresh, and logout.
- [WhatsApp Integration](./features/02-integrations/integration-whatsapp-spec.md) — Outbound messaging via Evolution API.
- [Dashboard & Analytics](./features/03-dashboard/dashboard-spec.md) — Usage metrics and reporting UI.

### 🛠️ DevOps & Infrastructure
- [Infrastructure Map](./devops/infrastructure-map.md) — Cloud and network layout.
- [Environment Variables](./devops/env-variables.md) — Configuration guide for all services.
- [Disaster Recovery](./devops/runbooks-disaster.md) — Critical incident procedures.

### 📐 Standards
- [Coding Standards](./standards/coding-standards.md)
- [Git Flow](./standards/git-flow.md)

### 🧪 QA & Testing
- [Test Plan](./qa-testing/test-plan.md) — Overall test strategy and coverage goals.
- [Acceptance Criteria](./qa-testing/acceptance-criteria.md)

### 🔌 API Reference
- [API List](./api/api-list.md) — All endpoints, methods, request/response contracts.
```
