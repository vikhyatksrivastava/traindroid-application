# UI Testing Tool — Project Backlog

> Last updated: 2026-05-03  
> Status key: [ ] todo | [~] in progress | [x] done | [?] blocked/needs clarification

---

## Phase 0 — Project Setup
- [ ] Answer clarifying questions (device target, APK source, report format, deployment, CI/CD, timeline)
- [ ] Finalize architecture decision (monorepo vs separate services)
- [ ] Initialize Git repository
- [ ] Set up Docker Compose skeleton (Node frontend, Python test runner)
- [ ] Configure project linting and formatting (ESLint, Black/Ruff)
- [ ] Write CLAUDE.md with project conventions

---

## Phase 1 — APK Upload & Device Management
- [?] APK upload endpoint (Node/Express or FastAPI)
- [?] APK storage (local volume or object storage)
- [?] Android device/emulator provisioning in Docker
- [?] ADB connectivity layer (adb connect, device health check)

---

## Phase 2 — Screen Flow Testing
- [?] Define flow definition format (JSON/YAML spec for navigation steps)
- [?] Flow execution engine (Python + UIAutomator2 or Appium)
- [?] Pass/fail detection per step
- [?] Screenshot capture on failure
- [?] PyTest integration for flow test cases

---

## Phase 3 — Functionality Testing
- [?] Define functionality test spec format
- [?] Input simulation (tap, swipe, type)
- [?] Assertion engine (element presence, text match, state checks)
- [?] PyTest test runner integration

---

## Phase 4 — Reporting & Audit
- [?] Test result aggregation service
- [?] Audit document generation (HTML/PDF)
- [?] Admin approval workflow (web UI for approve/reject)
- [?] Final test report generation post-approval
- [?] Report export (PDF / email / dashboard)

---

## Phase 5 — Portal UI
- [?] Web portal frontend (React or plain HTML/JS via Node)
- [?] APK upload screen
- [?] Test run configuration screen
- [?] Live test run progress view
- [?] Results / audit review screen
- [?] Admin approval screen

---

## Phase 6 — CI/CD Integration (if applicable)
- [?] Webhook / API trigger for test runs from CI
- [?] Status badge / callback on completion
- [?] GitHub Actions / Jenkins pipeline example

---

## Parking Lot (needs decision)
- Multi-APK regression comparison
- Role-based access (admin vs tester)
- Notification system (email/Slack on completion)
- Test scheduling / recurring runs
