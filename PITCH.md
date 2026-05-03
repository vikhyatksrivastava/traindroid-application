# traindroid — Autonomous Android UI Testing Platform
### Project Pitch Document · v1.0 · May 2026

---

## The Problem

Mobile QA is broken in most small-to-mid teams.

A developer pushes a build. A tester manually opens the app, taps through 10–15 screens, fills forms, checks flows, screenshots failures, writes a report — then does it again tomorrow for the next build. This is:

- **Slow** — 30–60 minutes per test run, per tester
- **Inconsistent** — humans miss things, especially on the 10th regression of the day
- **Unscalable** — one tester can do 3–5 runs a day maximum
- **Undocumented** — notes live in Slack threads and memory, not structured audit logs
- **Blocking** — a build cannot ship until a human has time to look at it

For teams building on **React Native / Expo**, this problem is compounded. The UI evolves fast. Screens change between builds. Any rule-based automation written today breaks on next week's update.

---

## The Solution

**traindroid** is a web portal that accepts an uploaded `.apk` file and autonomously tests it using an AI agent — no human intervention required.

A QA engineer (or the CI pipeline) uploads the APK. The platform:

1. Installs and launches the APK inside a headless Android emulator
2. Deploys an AI agent that reads the screen and navigates intelligently
3. Completes defined test goals (signup, login, core flows) without any pre-written scripts
4. Generates a structured audit log — every action, every decision, every screenshot
5. Delivers a pass/fail report for super admin approval before release sign-off

No emulator window. No human watching. Results in minutes, not hours.

---

## Why This Tool Is Worth Building

### 1. It replaces the most tedious part of QA — not the thinking part

This tool does not replace QA engineers. It eliminates the mechanical repetition: open app, tap through the same 12 screens, check the same 5 things. Engineers stay focused on exploratory testing, edge cases, and product decisions — the work that actually requires a human.

### 2. It adapts to UI changes automatically

Traditional test automation (Selenium, Appium scripts, UIAutomator rules) is brittle. Every selector, every hardcoded flow, every `d(text="Login").click()` breaks the moment a designer renames a button or restructures a screen.

This tool's AI agent reads the screen the way a human would — it sees "there's a login form here, I should fill in credentials" — not "find element with resourceId=login_button". A redesigned screen is handled automatically. No script maintenance.

### 3. The audit trail is a first-class citizen

Every step produces:
- A screenshot
- The AI's reasoning ("Detected signup screen because no session cookie was found")
- Action taken and outcome
- Pass/fail status with error details

This is structured, timestamped, and human-readable. Super admins get a document they can actually review — not just a green/red status light.

### 4. Zero-configuration for new apps

You do not write a single line of test code per app. Upload the APK, describe the goal in plain English ("Test the signup and login flow"), and the agent figures out the rest. This means the tool works on any Android app, not just yours.

### 5. It runs while you sleep

A test run takes 3–5 minutes. You can trigger it from CI on every push, run it overnight across multiple scenarios, or run it on-demand before a release. There is no bottleneck on human availability.

---

## How It Works — The Architecture

### The Pipeline

```
User uploads .apk via web portal
          │
          ▼
Node.js server stores file, triggers test runner
          │
          ▼
Android Emulator (headless, -no-window) boots in background
          │
          ▼
ADB installs APK → launches app
          │
          ▼
AI Agent loop begins:
  ┌─────────────────────────────────────────┐
  │  1. Capture screenshot + UI tree dump   │
  │  2. Send to LLM: "What should I do?"   │
  │  3. LLM returns: action + reasoning    │
  │  4. Execute action via ADB             │
  │  5. Log step to audit trail            │
  │  6. Repeat until goal complete         │
  └─────────────────────────────────────────┘
          │
          ▼
Audit document generated
          │
          ▼
Super admin reviews → approves → test report released
```

### Two Approaches Considered

#### Option A — Traditional Rule-Based (UIAutomator2 scripts)

Write explicit Python scripts for every flow:
```python
if d(text="Login").exists:
    d(text="Create Account").click()
    d(resourceId="email_field").set_text("test@test.com")
    ...
```

| Pros | Cons |
|------|------|
| Deterministic, predictable | Breaks on every UI change |
| No API cost | Must write scripts per app |
| Fast execution | Zero reasoning in audit log |
| Mature tooling | Cannot handle unknown screens |

#### Option B — AI Agent (Recommended)

An LLM reads the screen and decides actions, exactly as a human tester would:

```
Screenshot + UI accessibility tree
          │
          ▼
Claude / GPT-4o: "I see a signup form.
  Goal is to create an account.
  Action: fill email field with test@test.com"
          │
          ▼
ADB executes the tap/type
          │
          ▼
Audit entry: action + reasoning + screenshot
```

| Pros | Cons |
|------|------|
| Adapts to UI changes automatically | Small API cost per run |
| Works on any app, zero config | ~2–3s latency per step (vs 50ms) |
| Audit log contains human reasoning | Non-deterministic (AI can occasionally misread) |
| Handles unknown/new screens gracefully | Requires internet for API calls |
| No test script maintenance ever | |

#### Recommended: Hybrid Architecture

Use AI for the brain, ADB for the hands:

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

This gives the adaptability of AI with the reliability of direct device control.

### Tiered Model Strategy (Cost Optimisation)

Not every screen needs an expensive vision model. Use a tiered approach:

```
Tier 1 — Accessibility tree (text only) → Haiku / GPT-4o mini
  Handles: standard forms, buttons, navigation, text fields
  Cost: ~$0.02 per run
  Coverage: ~80% of screens

Tier 2 — Screenshot + tree (multimodal) → Claude Sonnet
  Handles: custom UI, image-heavy screens, canvas components
  Cost: ~$0.29 per run
  Triggered: only when Tier 1 returns low confidence

Tier 3 — Human review flag
  Handles: screens AI cannot interpret with confidence
  Cost: $0
  Triggered: when AI confidence is below threshold
```

**Effective average: ~$0.05–$0.07 per test run** with this strategy.

---

## Cost Breakdown

### Infrastructure Costs

| Component | Cost |
|-----------|------|
| Android Emulator (local machine / WSL2) | **$0** |
| Web portal hosting (local, Milestone 1) | **$0** |
| GitHub Actions CI (public repo) | **$0** |
| GitHub Actions CI (private repo) | ~$0.008/minute |

### AI API Costs Per Test Run

A typical run: 15 steps, accessibility tree + screenshot per step.

| Model | Strategy | Cost per run |
|-------|----------|--------------|
| Claude Haiku 4.5 | Text-only (Tier 1) | ~$0.02 |
| Claude Sonnet 4.6 | Vision + text (Tier 2) | ~$0.29 |
| GPT-4o mini | Text-only (Tier 1) | ~$0.01 |
| GPT-4o | Vision + text (Tier 2) | ~$0.23 |
| **Hybrid (recommended)** | **80% Haiku, 20% Sonnet** | **~$0.07** |

> Prices are approximate and subject to change. Verify at provider pricing pages before production budgeting.

### Monthly Cost Projection

| Usage | Runs/day | Monthly cost (hybrid) |
|-------|----------|-----------------------|
| Small team, pre-release only | 5 | **~$10** |
| Active sprint, daily testing | 20 | **~$42** |
| CI on every PR (busy team) | 50 | **~$105** |

### ROI vs Manual QA

| | Manual Tester | traindroid |
|---|---|---|
| Time per run | 30–60 min | 3–5 min |
| Cost per run | $5–$15 | $0.02–$0.29 |
| Runs per day | 3–5 (human limit) | Unlimited |
| Overnight runs | Not possible | Fully automated |
| Audit documentation | Manual, inconsistent | Automatic, structured |
| Works on new app | Immediate | Immediate (no script writing) |
| **Annual cost (20 runs/day)** | **$36,000–$110,000** | **~$500** |

---

## Milestone Plan

### Milestone 1 — MVP (Week 1)
**Goal:** Prove the core loop works end-to-end.

- [ ] APK upload via web portal
- [ ] Headless Android emulator boot (WSL2 + AVD locally)
- [ ] AI agent navigates signup + login flow autonomously
- [ ] Basic audit log (action + screenshot per step)
- [ ] Simple pass/fail report on portal

**Stack:** Node.js portal · Python AI runner · UIAutomator2 · Claude Haiku (text) · WSL2

### Milestone 2 — Hardening (Week 2–3)
- [ ] Vision model fallback (Sonnet) for complex screens
- [ ] Multi-flow support (configurable goals)
- [ ] Super admin approval workflow
- [ ] Structured PDF/HTML report export
- [ ] Docker containerisation of test runner

### Milestone 3 — Scale (Month 2)
- [ ] GitHub Actions CI integration (trigger on push)
- [ ] Regression comparison (M1 baseline vs current build)
- [ ] Multiple concurrent test runs
- [ ] Role-based portal access (admin / tester)
- [ ] Notification system (Slack / email on completion)

---

## Honest Risks and Limitations

This is a pitch, not a sales deck. Here are the real risks:

| Risk | Likelihood | Mitigation |
|------|-----------|------------|
| AI misreads an unusual screen | Medium | Confidence threshold → human review flag |
| Emulator boot is slow (AVD takes 60–90s) | High (it just is) | Pre-warm emulator; keep alive between runs |
| APK requires Google Play Services | Medium | Use an AVD image with Play Services |
| AI makes a wrong action and gets stuck | Low | Max-steps limit + stuck-screen detection |
| API costs spike on complex apps | Low | Tier 1 first, cap daily API spend |
| Android version fragmentation | Medium | Test against API 31+ (covers 80% of devices) |
| React Native / Expo custom components hide from accessibility tree | Medium | Vision fallback handles this |

---

## Why Now

- **Multimodal LLMs are mature enough.** Six months ago this was a research project. Today Claude Sonnet and GPT-4o reliably interpret mobile UI screenshots with high accuracy.
- **The tooling exists.** UIAutomator2, ADB, AVD — all battle-tested. The AI layer sits on top of proven infrastructure.
- **The problem is not going away.** As mobile teams ship faster, manual QA becomes more of a bottleneck, not less.
- **No credible self-hosted solution exists.** Firebase Test Lab and AWS Device Farm require scripts. Appium requires maintenance. An autonomous AI-powered portal with a clean upload interface does not exist as a self-hostable open tool.

---

## The Ask

**One week. One developer. One working end-to-end prototype.**

The Milestone 1 MVP demonstrates:
- Upload an APK
- Watch the AI agent test it autonomously
- Receive a structured audit report

If the core loop works — and the technology is ready for it to work — the remaining milestones are integration and polish, not invention.

---

*Built with: Node.js · Python · UIAutomator2 · Claude API · Android SDK · Docker · GitHub Actions*
