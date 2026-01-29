# 🎓 Lesson 10: Digital Provenance - Proving Code Hasn't Been Tampered With

## 🛡️ Welcome Back, AI-DevSecOps Analyst!

Ever watched a crime show where they talk about the "chain of custody" for evidence? 🔍 Today we're learning about **Digital Provenance** - the same concept, but for code!

### 🎯 What You'll Learn

By the end of this lesson, you'll understand:
- What "provenance" means and why it matters
- How we create tamper-proof records
- How to detect if code has been secretly changed
- The concept of "Shadow Code" (unauthorized AI changes)

---

## 🤔 What is Provenance? (The Crime Show Analogy)

**Provenance** answers the question: **"Where did this come from, and can we prove it hasn't been changed?"**

### The Crime Scene Evidence Analogy

```
┌─────────────────────────────────────────────────────────────────┐
│                    CHAIN OF CUSTODY                              │
│                                                                  │
│  Crime Scene → Evidence Bag → Lab → Court                       │
│       │              │         │       │                         │
│    "Found at        "Sealed   "Tested" "Presented"              │
│     2:30 PM"         by               as                         │
│                    Officer            evidence"                  │
│                    Smith"                                        │
│                                                                  │
│  At EVERY step, we document:                                     │
│  - WHO handled it                                                │
│  - WHEN they handled it                                          │
│  - WHAT state it was in                                          │
│                                                                  │
│  If anyone breaks the chain → Evidence can't be trusted!        │
└─────────────────────────────────────────────────────────────────┘
```

### Applied to Code

```
┌─────────────────────────────────────────────────────────────────┐
│                    CODE CHAIN OF CUSTODY                         │
│                                                                  │
│  Developer Writes → Security Scan → Human Approves → Deployed   │
│         │                 │               │              │       │
│    "Created by        "Scanned by    "Approved by   "Same code  │
│     Alice at          Bot at 2:31,    Bob at 2:35"   deployed"  │
│     2:30 PM"          0 violations"                              │
│                                                                  │
│  At EVERY step, we record a HASH (digital fingerprint):         │
│  - Hash 1: abc123... (original)                                  │
│  - Hash 2: abc123... (after scan - same = not changed!)         │
│  - Hash 3: abc123... (after approval - same = not changed!)     │
│                                                                  │
│  If ANY hash changes → Code was tampered with!                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔐 What is a Hash? (The Fingerprint)

A **hash** is like a digital fingerprint for any piece of text or file:

```
┌─────────────────────────────────────────────────────────────────┐
│                    HOW HASHING WORKS                             │
│                                                                  │
│  INPUT: "Hello World"                                            │
│  HASH:  a591a6d40bf420404a011733cfb7b190d62c65bf0bcda32b57b277d9ad9f146e │
│                                                                  │
│  INPUT: "Hello World!" (just added an exclamation mark)         │
│  HASH:  7f83b1657ff1fc53b92dc18148a1d65dfc2d4b1fa3d677284addd200126d9069 │
│                                                                  │
│  COMPLETELY DIFFERENT! 🤯                                        │
│                                                                  │
│  Key Properties:                                                 │
│  ✅ Same input → Always same hash (reliable)                    │
│  ✅ Tiny change → Completely different hash (sensitive)         │
│  ✅ Can't reverse it (one-way only)                             │
│  ✅ Practically impossible to fake (secure)                     │
└─────────────────────────────────────────────────────────────────┘
```

### Real-World Analogy: The Wax Seal

```
Medieval Letter:
- You write a letter
- You seal it with hot wax and press your ring into it
- Anyone who opens it BREAKS the seal
- They can't recreate your unique ring impression

Digital Hash:
- You write code
- You create a hash (the "seal")
- Anyone who changes the code changes the hash
- They can't recreate the original hash without the original code
```

---

## 📋 The SOC Ledger: Our Record Book

Our **SOC Ledger** (Security Operations Center Ledger) is like an **unchangeable record book** that tracks everything:

```
┌─────────────────────────────────────────────────────────────────┐
│                    SOC LEDGER ENTRIES                            │
│                                                                  │
│  Entry #1 - 2026-01-21 14:30:00                                  │
│  ─────────────────────────────────                              │
│  File: app.py                                                    │
│  Action: SCANNED                                                 │
│  Agent: windsurf-cascade (AI)                                    │
│  Result: 0 violations                                            │
│  Content Hash: abc123def456...                                   │
│  Human Approval: None yet                                        │
│                                                                  │
│  Entry #2 - 2026-01-21 14:35:00                                  │
│  ─────────────────────────────────                              │
│  File: app.py                                                    │
│  Action: APPROVED                                                │
│  Approved By: bob@company.com                                    │
│  Content Hash: abc123def456... (SAME = not changed!)            │
│  Links To: Entry #1                                              │
│                                                                  │
│  Entry #3 - 2026-01-21 15:00:00                                  │
│  ─────────────────────────────────                              │
│  File: app.py                                                    │
│  Action: SCANNED                                                 │
│  Content Hash: xyz789... (DIFFERENT! Someone changed it!)       │
│  Status: ⚠️ SHADOW CODE DETECTED                                │
└─────────────────────────────────────────────────────────────────┘
```

---

## 👻 Shadow Code: The Invisible Threat

**Shadow Code** is one of the scariest things in AI-DevSecOps:

```
┌─────────────────────────────────────────────────────────────────┐
│                    WHAT IS SHADOW CODE?                          │
│                                                                  │
│  Shadow Code = Code changed by AI WITHOUT human approval        │
│                                                                  │
│  Scenario:                                                       │
│  1. Human writes app.py, gets it approved ✅                    │
│  2. AI agent "helpfully" modifies app.py later 🤖               │
│  3. No one re-scans or re-approves                              │
│  4. Modified code goes to production 😱                          │
│                                                                  │
│  The Problem:                                                    │
│  - AI might have introduced vulnerabilities                      │
│  - No human reviewed the changes                                 │
│  - The audit trail was broken                                    │
│                                                                  │
│  This is why we HASH everything and check provenance!           │
└─────────────────────────────────────────────────────────────────┘
```

### How We Detect Shadow Code

```
VERIFICATION PROCESS:

Step 1: Read the current file
        "What's in app.py right now?"

Step 2: Hash the contents
        Current hash: xyz789...

Step 3: Check the SOC Ledger
        "What was the last APPROVED hash for app.py?"
        Approved hash: abc123...

Step 4: Compare
        xyz789... ≠ abc123...

Step 5: ALERT! 🚨
        "File was modified since approval!"
        "This is SHADOW CODE - unauthorized change detected!"
```

---

## 🔗 The Chain: How Records Link Together

Each record in our ledger **links to the previous one**, creating an unbreakable chain:

```
┌─────────────────────────────────────────────────────────────────┐
│                    PROVENANCE CHAIN                              │
│                                                                  │
│  [Record 1] ──────────────────────────────────────────────────► │
│  │ File: app.py                                                  │
│  │ Hash: abc123...                                               │
│  │ Parent: None (first record)                                   │
│  │                                                               │
│        ↓ (links to)                                              │
│                                                                  │
│  [Record 2] ──────────────────────────────────────────────────► │
│  │ File: app.py                                                  │
│  │ Hash: abc123... (same content)                                │
│  │ Parent: Record 1's hash                                       │
│  │ Approved By: Alice                                            │
│  │                                                               │
│        ↓ (links to)                                              │
│                                                                  │
│  [Record 3] ──────────────────────────────────────────────────► │
│  │ File: app.py                                                  │
│  │ Hash: def456... (content changed - new version)              │
│  │ Parent: Record 2's hash                                       │
│  │ Approved By: Bob                                              │
│                                                                  │
│  WHY THIS MATTERS:                                               │
│  If someone tries to DELETE or MODIFY Record 2,                 │
│  Record 3's "Parent" link would point to something that         │
│  doesn't exist or is wrong → TAMPERING DETECTED!                │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔍 Verification: Trust But Verify

When we want to deploy code, we verify its provenance:

```
DEPLOYMENT CHECK:

┌─────────────────────────────────────────────────────────────────┐
│  "Is app.py safe to deploy?"                                     │
│                                                                  │
│  ✅ Check 1: Was it scanned?                                     │
│     → Yes, at 14:30:00                                           │
│                                                                  │
│  ✅ Check 2: Did the scan pass?                                  │
│     → Yes, 0 CRITICAL violations                                 │
│                                                                  │
│  ✅ Check 3: Was it approved by a human?                         │
│     → Yes, by bob@company.com at 14:35:00                        │
│                                                                  │
│  ✅ Check 4: Is the current file the same as what was approved? │
│     → Current hash: abc123...                                    │
│     → Approved hash: abc123...                                   │
│     → MATCH! ✅                                                  │
│                                                                  │
│  RESULT: ✅ VERIFIED - Safe to deploy!                          │
└─────────────────────────────────────────────────────────────────┘
```

### What If Verification Fails?

```
FAILED VERIFICATION:

┌─────────────────────────────────────────────────────────────────┐
│  "Is app.py safe to deploy?"                                     │
│                                                                  │
│  ✅ Check 1: Was it scanned? → Yes                               │
│  ✅ Check 2: Did the scan pass? → Yes                            │
│  ✅ Check 3: Was it approved? → Yes                              │
│  ❌ Check 4: Current hash matches approved hash?                 │
│     → Current: xyz789...                                         │
│     → Approved: abc123...                                        │
│     → NO MATCH! ❌                                               │
│                                                                  │
│  RESULT: 🚨 BLOCKED - Shadow code detected!                     │
│                                                                  │
│  Actions:                                                        │
│  1. Alert the security team                                      │
│  2. Identify who/what changed the file                          │
│  3. Re-scan and re-approve before deploying                     │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📊 Why This Matters: Real Scenarios

### Scenario 1: The Insider Threat

```
Without Provenance:
- Developer changes approved code at midnight
- Adds a backdoor to steal data
- Deploys to production
- No one knows until data breach occurs

With Provenance:
- Developer changes approved code at midnight
- Tries to deploy
- System: "Hash mismatch! Code changed since approval!"
- Deployment BLOCKED
- Alert sent to security team
- Backdoor caught before damage done ✅
```

### Scenario 2: The Compromised AI

```
Without Provenance:
- AI agent gets tricked by prompt injection
- Silently modifies security-critical file
- Change goes unnoticed
- Vulnerability in production

With Provenance:
- AI agent modifies file
- Next scan detects hash changed
- "Shadow Code Alert: File modified without approval"
- Change quarantined for review
- AI's mistake caught ✅
```

---

## 🎯 Check for Understanding

**Question 1**: Why can't an attacker just recalculate the hash after changing the code?

*Hint: Think about the "parent hash" linking...*

**Question 2**: What's the difference between a "scan passing" and "provenance being verified"?

*Hint: One checks for bugs, the other checks for unauthorized changes...*

---

## 📚 Interview Prep

**Q: What is digital provenance and why is it important?**

**A**: "Digital provenance is a cryptographic chain of custody that proves code hasn't been tampered with since it was approved. It's important because:
1. It detects unauthorized changes (shadow code)
2. It creates an audit trail for compliance
3. It links human approvals to specific versions of code
4. It catches insider threats and compromised AI agents"

**Q: How is provenance different from version control (Git)?**

**A**:

| Feature | Git | Provenance Chain |
|---------|-----|------------------|
| **Purpose** | Track code history | Track security approvals |
| **Records** | Who changed what | Who approved it as secure |
| **Focus** | Development workflow | Security compliance |
| **Links** | Code versions | Approval decisions |

"Git tells you WHO changed WHAT. Provenance tells you WHO APPROVED that it's SECURE. You need both!"

**Q: What is "Shadow Code"?**

**A**: "Shadow Code is code that was modified without proper human approval - usually by an AI agent acting autonomously. It's dangerous because the changes weren't reviewed for security issues. We detect it by comparing the current file hash against the last approved hash in our SOC Ledger."

---

## 🚀 Ready for Lesson 11?

In the next lesson, we'll explore **SOC Observability** - how we monitor everything happening in our security system, like a control room watching all the cameras!

*Remember: Trust is earned through verification. If you can't prove where code came from, you can't trust where it's going!* 🛡️🔐

