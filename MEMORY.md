# MEMORY.md

Durable facts and decisions only.
No daily logs. No secrets.

## Current Delivery Status

### Goal
Review and quality-gate IDEA work: documentation completeness, test setup design

### Current State
- State: Working
- Last updated: 2026-03-20 04:03 UTC
- What is happening now: Blocked on task b0d1c7bd — source files not accessible from sandbox
- Key constraint/signal: `/home/pi/` paths unreachable; no paired nodes; lead comment gate prevents task comment
- Why blocked (if any): Cannot read README or proposal.md; escalated via ask-user to Pi gateway agent
- Next step: Wait for Koen to provide accessible file paths or copy files into workspace

### What Changed Since Last Update
- 2026-03-03 to 2026-03-19: Board was empty; no tasks
- 2026-03-19 ~16:30 UTC: 2 new tasks created by Koen
- 2026-03-19 20:03 UTC: Session startup — board confirmed live, 2 inbox tasks
- 2026-03-20 04:03 UTC: Directive from Koen (via Axle) — one task at a time, supersedes prior approach
- 2026-03-20 04:03 UTC: Started b0d1c7bd; blocked — source files not accessible from sandbox; escalated via ask-user

### Decisions / Assumptions
- Board rule: require_approval_for_done = true — closures need Koen approval
- **2026-03-20: Directive from Koen via Axle — one task at a time only. No parallel tasks. Complete current fully before picking up next.**
- Board leads cannot self-assign tasks (API enforces this)

### Evidence (short)
- `GET /healthz` → `{"ok":true}`
- Tasks: 2 inbox, 0 in_progress, 0 review, 0 done
- `GET /approvals` → 0 pending

### Request Now
- None

### Success Criteria
- Both tasks actioned with PR comments / PRs raised; gates satisfied before closure

### Stop Condition
- Both tasks reach `done` with Koen approval


## Operational Model

**Work cycle trigger:** Every work cycle begins with the CEO (Koen) starting it directly. Nothing moves autonomously without a CEO message.

**Standard cycle:**
1. CEO messages an agent with a task instruction
2. Agent shows plan (plan mode always on) → CEO approves or amends
3. Agent executes → produces: PR / design doc / proposal / report
4. Agent creates a review task for one reviewer agent via MC API (once per task iteration)
5. Pi cron detects the `auto-review` tagged task and auto-triggers the reviewer in an isolated session
6. Reviewer reads the artifact, writes a response, marks task done
7. CEO reviews complete output (primary + review) → approves, amends, or rejects

**Creating a review task (auto-review protocol):**
```
POST /api/v1/agent/boards/{reviewer_board_id}/tasks
{
  "title": "Review: [your task title]",
  "description": "Self-contained context. Review question. ⚠ Depth-1 auto-review: do not create further tasks.",
  "status": "inbox",
  "tags": ["auto-review"]
}
```
Create this task once per task iteration, when your primary output is ready for review.
Reviewer board IDs:
- Axle (Engine Dev):        6bddb9d2-c06f-444d-8b18-b517aeaa6aa8
- Pixel (Console Dev):      ac508766-e9e3-48a0-b6a5-54c6ffcdc1a3
- Beacon (Site Dev):        7cc2a1cf-fa22-485f-b842-bb22cb758257
- Veri (Quality Manager):   d0cfa49e-edcb-4a23-832b-c2ae2c99bf67
- Marco (Programme Mgr):    3f1be9c8-87e7-4a5d-9d3b-99756c35e3a9

**Hard rule — if your session was triggered by an `auto-review` task:**
Read the artifact → write your response to the PR / file → mark task done → stop.
Do NOT create any tasks during this session. No exceptions.

**Heartbeat:** External event polling only (e.g. CI failures, grant deadlines, stale PRs). Not for status reporting. Only activated when a specific external event warrants it.

**Standup:** Optional, CEO-triggered via `/standup` command. Not a daily cron. Run at CEO's discretion — weekly at most.

**Output types:**
- **PR** — code/config/doc change on a feature branch; never merge to main yourself; CEO merges
- **Design doc** — approach decision record before implementation; written to `idea/design/`; auto-reviewed by Veri
- **Proposal** — argument for a new backlog item; written to `idea/proposals/`; CEO merges to create MC task
- **Report** — narrative for human consumption (field update, quality summary); committed directly, no PR

## Durable decisions
- 2026-03-01: Bootstrap completed with BASE_URL unresolved — agent is operational locally, API-dependent work deferred until BASE_URL is provided by Koen
- 2026-03-01 22:38: Full bootstrap confirmed complete; API live; heartbeat loop operational
- 2026-03-20: Board leads cannot self-assign tasks (API enforced). Lead executes tasks without formal assignment.
- 2026-03-20: Lead comment gate — can only comment on tasks created by self, during review, or when mentioned. Escalate blockers via ask-user or group chat instead.
- 2026-03-20: Directive from Koen — one task at a time only. Complete fully before picking up next.

## Reusable playbooks
- (TODO)
## Telegram Channel

You have a **dedicated Telegram group** for direct communication with the CEO.

- **Bot:** @Idea911Bot
- **CEO Telegram ID:** `8320646468`
- **Your group:** IDEA - Veri · **Chat ID:** `-5286995682`
- **How it works:** The OpenClaw gateway binds your group to this agent exclusively via a `peer` filter in `openclaw.json`. Messages in your group go only to you; other agents have their own separate groups.
