# traindroid

**Autonomous Android UI Testing Platform**

Upload an `.apk`. An AI agent installs it in a headless emulator, navigates the app intelligently, and delivers a structured audit report — no scripts, no human watching.

---

## The Problem

Mobile QA on fast-moving React Native / Expo apps is slow, inconsistent, and undocumented. Manual testers spend 30–60 minutes per run tapping through the same screens. Rule-based automation (Appium, UIAutomator scripts) breaks every time a designer renames a button.

traindroid replaces the mechanical repetition with an AI agent that reads the screen the way a human does.

---

## How It Works

```
User uploads .apk via web portal
        │
        ▼
Headless Android Emulator boots (AVD, -no-window)
        │
        ▼
ADB installs APK → launches app
        │
        ▼
AI Agent loop:
  1. Capture screenshot + UI accessibility tree
  2. Send to LLM: "What screen is this? What should I do?"
  3. LLM returns: action + reasoning
  4. Execute action via ADB / UIAutomator2
  5. Log step to audit trail
  6. Repeat until goal complete
        │
        ▼
Structured audit document generated
        │
        ▼
Super admin reviews → approves → test report released
```

### Architecture

```
AI Orchestrator (Claude)         ← decides WHAT to do
        │
        │  structured actions
        ▼
ADB Executor (UIAutomator2)      ← executes deterministically
        │
        ▼
Android Emulator                 ← runs the APK
```

### Tiered Model Strategy

| Tier | Input | Model | Cost/run | Coverage |
|------|-------|-------|----------|----------|
| 1 | Accessibility tree (text) | Claude Haiku | ~$0.02 | ~80% of screens |
| 2 | Screenshot + tree (vision) | Claude Sonnet | ~$0.29 | complex/custom UI |
| 3 | Human review flag | — | $0 | low-confidence screens |

**Effective average: ~$0.05–$0.07 per test run** with the hybrid strategy.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Web portal | Node.js |
| AI test runner | Python |
| Device control | UIAutomator2 · ADB · Android SDK |
| AI models | Claude API (Haiku + Sonnet) |
| Emulator | AVD (WSL2 / local) |
| Containerisation | Docker |
| CI/CD | GitHub Actions |

---

## Milestone Plan

### Milestone 1 — MVP
- [ ] APK upload via web portal
- [ ] Headless Android emulator boot (WSL2 + AVD)
- [ ] AI agent navigates signup + login flow autonomously
- [ ] Basic audit log (action + screenshot per step)
- [ ] Simple pass/fail report on portal

### Milestone 2 — Hardening
- [ ] Vision model fallback (Sonnet) for complex screens
- [ ] Multi-flow support (configurable test goals)
- [ ] Super admin approval workflow
- [ ] Structured PDF/HTML report export
- [ ] Docker containerisation of test runner

### Milestone 3 — Scale
- [ ] GitHub Actions CI integration (trigger on push)
- [ ] Regression comparison across builds
- [ ] Multiple concurrent test runs
- [ ] Role-based portal access (admin / tester)
- [ ] Notification system (Slack / email on completion)

---

## Cost vs Manual QA

| | Manual Tester | traindroid |
|---|---|---|
| Time per run | 30–60 min | 3–5 min |
| Cost per run | $5–$15 | ~$0.07 |
| Runs per day | 3–5 (human limit) | Unlimited |
| Audit documentation | Manual, inconsistent | Automatic, structured |
| **Annual cost (20 runs/day)** | **$36,000–$110,000** | **~$500** |

---

## Project Docs

- [PITCH.md](PITCH.md) — full project pitch and architecture rationale
- [BACKLOG.md](BACKLOG.md) — phased backlog
- [competitive-analysis.md](competitive-analysis.md) — market landscape and positioning
- [context.md](context.md) — initial requirements and Q&A

---

*Status: Pre-development — planning and architecture phase*
