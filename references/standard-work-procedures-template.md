# standard-work-procedures.md Template

`standard-work-procedures.md` defines how the team builds, reviews, and ships. It is the first file an agent reads before starting any task. It must be accurate — outdated procedures are worse than none.

**Rules:**
- Only created or updated via `/standard` or explicit user instruction. It is a protected file.
- Must be specific enough to follow. Avoid vague statements like "write clean code."
- Covers at minimum: development lifecycle, branching strategy, commit format, documentation obligations, and Definition of Done.
- Additional sections (deployment process, code review standards, testing requirements, etc.) are added based on the project's actual workflow.

---

## Template

```markdown
*Last Updated: YYYY-MM-DD*
---
# 🛠️ Standard Work Procedures

This document defines how we build, review, and ship. Read before starting any task.

## 🚀 Feature Development Lifecycle
1. **Backlog:** Review the planning spec in `/planning-specifications/` (read-only — do not edit). Create a personal to-do list based on it.
2. **Sync:** Read the feature spec in `/docs/features/`. If it doesn't exist yet, create it.
3. **Branch:** Create a branch following the [Git Flow](./standards/git-flow.md).
4. **Code:** Follow [Coding Standards](./standards/coding-standards.md).
5. **Test:** Every feature must have a test plan. Run the suite before opening a PR.
6. **Document:** Update the feature spec and test doc in the same PR as the code change.
7. **Review:** PR requires at least one peer approval before merge.
8. **Commit:** Update `changelog.md` at commit time — not before.

## 🌿 Branching Strategy
- `main` — production. Never commit directly.
- `dev` — integration branch. All feature branches merge here first.
- Feature branches: `feature/short-description`
- Fix branches: `fix/short-description`
- Release branches: `release/vX.Y.Z`

## 📝 Commit Format
Commits must be concise and objective.
- Format: `[type(scope)]: message` or for monorepos: `[area/type]: message`
- Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`
- Examples:
  - `[feat(auth)]: add JWT refresh token rotation`
  - `[fix(whatsapp)]: resolve media upload timeout`
  - `[docs]: update auth-spec with MFA behavior`

## 🧪 Testing & QA
- Minimum 80% coverage for all new logic.
- Every feature must have a corresponding test doc (`feature-test.md`) updated before PR merge.

## 📝 Documentation Obligations
- Every code change must have a corresponding doc update in the same PR.
- If you change a feature's behavior, update its spec. No exceptions.
- `doc-map.md` must be updated whenever a file or folder is added, renamed, or removed.
- `changelog.md` is only updated at commit time — never during active development.

## ✅ Definition of Done (DoD)
A task is done when ALL of the following are true:
- [ ] Code is linted and formatted.
- [ ] Unit/Integration tests pass (coverage ≥ 80% for new code).
- [ ] Feature spec updated (`Last Updated` date is current and content is accurate).
- [ ] `doc-map.md` updated if structure changed.
- [ ] PR approved by at least one peer.
- [ ] `changelog.md` updated at commit.
```

---

## Worked Example

```markdown
*Last Updated: 2026-04-13*
---
# 🛠️ Standard Work Procedures

This document defines how we build, review, and ship. Read before starting any task.

## 🚀 Feature Development Lifecycle
1. **Backlog:** Review the planning spec in `/planning-specifications/` (read-only). Create a personal to-do list based on it.
2. **Sync:** Read the feature spec in `/docs/features/`. Create it if it doesn't exist.
3. **Branch:** Create a branch following the [Git Flow](./standards/git-flow.md).
4. **Code:** Follow [Coding Standards](./standards/coding-standards.md).
5. **Test:** Every feature must have a corresponding test plan or automated suite.
6. **Document:** Update the feature spec and test doc in the same PR as the code.
7. **Review:** PR approved by at least one peer before merge.
8. **Commit:** Update `changelog.md` at commit time.

## 🌿 Branching Strategy
- `main` — production. Direct commits are not allowed.
- `dev` — integration. All features merge here before going to `main`.
- Feature branches: `feature/short-description` (e.g., `feature/whatsapp-media-templates`)
- Fix branches: `fix/short-description` (e.g., `fix/token-expiry-race-condition`)
- Releases: `release/v1.2.0`

## 📝 Commit Format
Commits must be concise and objective.
- Format: `[type(scope)]: message`
- Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`
- Examples:
  - `[feat(whatsapp)]: add support for interactive button templates`
  - `[fix(auth)]: resolve race condition on simultaneous registration`
  - `[refactor(dashboard)]: extract analytics query into service layer`

## 🔍 Code Review Standards
- PRs must not exceed 400 lines of change. Split larger work into smaller PRs.
- Every PR must include a description explaining *what* changed and *why*.
- Reviewer checks: correctness, test coverage, documentation update, and no hardcoded secrets.

## 🧪 Testing & QA
- Minimum 80% coverage for all new logic.
- Every feature must have a corresponding `feature-test.md` updated before PR merge.
- Run the full test suite locally before opening a PR.

## 📝 Documentation Obligations
- Every code change must have a corresponding doc update in the same PR. No exceptions.
- Changing a feature's behavior → update its spec.
- Adding or removing a file → update `doc-map.md`.
- `changelog.md` is written at commit time only.

## 🚢 Deployment Process
- Deployments happen from `main` only, via the CI/CD pipeline.
- Every release must have a version tag (`vX.Y.Z`) and a corresponding `changelog.md` entry.
- Rollback: revert the merge commit and redeploy. Do not patch production directly.

## ✅ Definition of Done (DoD)
A task is done when ALL of the following are true:
- [ ] Code is linted and formatted with no warnings.
- [ ] Unit and integration tests pass (coverage ≥ 80% for new code).
- [ ] Feature spec updated — content is accurate, not just date-bumped.
- [ ] `doc-map.md` updated if files or folders were added or removed.
- [ ] PR description filled in and at least one peer approval received.
- [ ] `changelog.md` updated at commit time.
```
