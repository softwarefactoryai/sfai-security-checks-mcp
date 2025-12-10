# MCP Server Guardian — Security Checks

A lightweight security gate to quickly decide **whether it’s safe to execute** a third-party repository (especially “live demo” / hiring assignments).

This repo is designed to be used **agent-first**: you provide an agent with the validation playbook in `security-checks.md`, plus the repo to review, and it returns a **Go / No-Go** decision with evidence.

---

## What it does
- Produces a **GO / NO-GO** recommendation
- Explains **why** (risk signals + impacted assets)
- Suggests minimal safe execution steps (VM/sandbox, least privilege, isolated secrets)

---

## How to use
1) Open `security-checks.md` (canonical validation rules + questions).  
2) Pass `security-checks.md` to your agent and ask it to apply the checks to the target repo (URL or local path).  
3) Review the agent’s **GO / NO-GO** + reasons.  
4) If anything is flagged → **don’t run it on your main machine** (use VM/sandbox or reject).

---

## Inputs for the agent
- `security-checks.md` (the validation playbook)
- Target repository (URL or local folder)
- Optional: your environment constraints (OS, browser/wallet usage, cloud accounts present)

---

## Expected output
- Decision: **GO** / **NO-GO** / **GO (only in isolated VM)**
- Findings: top risk signals + where they appear (files/scripts/behaviors)
- Impact: what could be exposed (secrets, keys, sessions, cloud access)
- Mitigations: minimal safe steps if you must proceed

---

## Intended use
Defensive security, due diligence, and security culture for engineering teams.

---

## Disclaimer
This provides risk signals, not guarantees. Always use isolation (VM/sandbox) and follow your organization’s security policy.
