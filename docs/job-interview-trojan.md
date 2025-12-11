# Anti “Job Interview Trojan” Checklist (2–5 minutes)

Use this when someone asks you to **clone/run a repo during an interview**, “debug live”, or “just run the demo”.  
Goal: decide **RUN / DON’T RUN / RUN ONLY IN ISOLATED SANDBOX**.

---

## 0) Golden Rule (Decision First)
**If you can’t clearly explain what the code will do, where it will connect, and what it will access, don’t run it.**  
If the other side pressures you or gets irritated → treat as a red flag and stop.

**Default stance:** *Run nothing outside a disposable VM/sandbox.*

---

## 1) Fast Red-Flag Scan (People & Process)
Mark any that apply:

- Camera off + vague identity + “internet issues” + avoids real technical questions
- Urgency: “run it now”, “just npm start”, “quick fix” / “it’s safe trust me”
- Pushes for elevated permissions: admin, antivirus off, “allow access”, “accept all”
- Refuses to explain why they need you to run it locally (instead of screenshare, logs, or static review)
- Asks for sensitive info: wallet history, seed phrases, API keys, cloud creds, private repos
- Asks you to install unusual tools, browser extensions, or remote access software

**If 2+ are true → DON’T RUN.**

---

## 2) Safe Setup (Before Touching the Repo)
If you must proceed for a legitimate reason:

- Use a **disposable VM / sandbox** (no personal accounts logged in)
- **No SSH keys, no cloud profiles, no browser wallet extensions**
- **No secrets in env files**
- Separate network if possible; monitor outbound connections
- Snapshot/restore capability enabled

**Never** run on your main workstation.

---

## 3) Repo Static Check (90 seconds)
You’re looking for “execution on install” + “hidden download/execute” + “persistence”.

### 3.1 package.json scripts (highest signal)
Open `package.json` and inspect `"scripts"`:

🚩 Red flags:
- `prepare`, `postinstall`, `preinstall`, `install`, `prestart`, `poststart`
- Commands that launch background services:
  - `nohup`, `start /b`, `&`, `pm2`, `screen`, `daemon`, `disown`
- Download + execute patterns:
  - `curl ... | sh`, `wget ... | bash`, `Invoke-WebRequest`, `powershell -enc`
- Obfuscation:
  - very long one-liners, `eval`, base64 blobs, `node -e "<huge string>"`

**If any install-time execution exists → RUN ONLY IN SANDBOX or DON’T RUN.**

### 3.2 Look for “loader” patterns in code
Search for:
- `new Function`, `eval`, `child_process.exec`, `spawn`, `execSync`
- `require('child_process')`, `fs.readFile` + zip/tar logic
- `axios/fetch` to unknown domains or IPs
- `ethers` / chain RPC reads used for **payload retrieval** (C2 via blockchain)
- `.ssh`, `.env`, `.aws`, browser profile folders, `.vscode`, `.zshrc`, `.bashrc`

**Any dynamic code execution from remote content → DON’T RUN.**

---

## 4) Network & Exfil Heuristics (60 seconds)
Before running anything, determine:
- What outbound endpoints will it talk to? (domains, IPs, RPC URLs)
- Does it collect system/user data? (home dir, username, OS, installed apps)
- Does it access browsers/IDEs?

🚩 Red flags:
- hardcoded IPs/domains, pastebins, gist, blockchain memo reads
- upload endpoints, “telemetry” without documentation
- “analytics” libraries wired before any UI renders

---

## 5) Minimal “Decision Matrix”
Choose one:

### ✅ RUN (rare)
Only if ALL are true:
- No install-time scripts that execute code
- No dynamic code execution (`eval/new Function`)
- No suspicious file access targets
- Clear README + legitimate org identity + normal interview flow

### ⚠️ RUN ONLY IN ISOLATED VM/SANDBOX (common)
If:
- Any uncertainty exists
- You need to validate behavior for a real company and can do so safely

### ❌ DON’T RUN (recommended when pressured)
If:
- Social red flags + suspicious scripts/obfuscation
- Dynamic payload execution
- Attempts to access secrets, wallets, SSH keys, browser data
- Any pressure to disable protections

---

## 6) If You Already Ran It (Immediate Containment)
1. Disconnect network (or isolate host)
2. Kill suspicious processes (node/python/powershell/bash)
3. Remove persistence:
   - check shell profiles: `~/.bashrc`, `~/.zshrc`
   - check unusual files under `~/.vscode`, startup items, cron jobs
4. Assume compromise:
   - rotate passwords/tokens/API keys
   - rotate SSH keys
   - review cloud IAM activity (AWS/GCP/Azure), GitHub tokens
5. Move crypto operations to a clean device if wallets/extensions existed

---

## 7) “Safe Interview Alternatives” (Professional Pushback Script)
Offer one of these instead of running:
- “I can do a **static code review** and walk through risks.”
- “I’ll run it in a **disposable VM** with no secrets. If that’s not acceptable, we should stop.”
- “Share **logs**, a deployed demo URL, or a recording. I’m not executing third-party code on my workstation.”

If they react emotionally → that’s your answer.

---

## 8) What Good Looks Like (Legit Signals)
- Clear company domain email + verifiable employees
- Normal interview structure: questions, design discussion, code review, not “run this now”
- Repo has:
  - clean scripts, no install-time execution
  - clear README, license, contributors, issues
  - no obfuscated code
  - minimal permissions and transparent endpoints

---

## 9) Quick Search Keywords (for your MCP/Agent/IDE)
`postinstall`, `prepare`, `nohup`, `start /b`, `pm2`, `disown`, `curl | sh`, `powershell -enc`,  
`new Function`, `eval`, `child_process`, `execSync`, `.ssh`, `.env`, `.aws`, `.vscode`, `chrome`, `wallet`, `metamask`, `phantom`, `keychain`, `seed`

---

## Final Reminder
**The best security control is refusing unsafe execution.**  
A legit team won’t be offended by basic verification.
