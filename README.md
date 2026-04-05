# ⚡ Token Optimiser

> **Reduce your AI prompt token usage before sending to paid APIs — free, open-source, runs entirely in your browser.**

No LLM. No API calls. No sign-up. No installation. Pure JavaScript.


---

## Why This Exists

Every word you send to GPT-4, Claude, or Gemini is metered and billed. A typical verbose prompt from a SOC analyst investigating a SIEM alert uses **~237 tokens**. The same question, written with prompt discipline, uses **~31 tokens**.

Same question. Same AI answer. **87% cheaper.**

At scale — 200 queries a day across a 10-person team — poor prompt hygiene burns over **10 million tokens a month** on filler words that contribute nothing to the quality of the answer.

Token Optimiser is the pre-processing layer you run *before* your paid AI. Paste your prompt in, compress it, review what changed, then send the leaner version.

---

## Features

| Feature | Description |
|---|---|
| 💡 **Light mode** | Whitespace cleanup + filler phrase removal |
| ⚙️ **Medium mode** | Adds stopword removal + duplicate sentence detection |
| 🔥 **Aggressive mode** | Full compression — adj/adv stripping + bullet conversion |
| ⚡ **Structure mode** | Rewrites verbose text into labelled `[ALERT]` / `[ASK]` / `[PRIORITY]` sections |
| 💰 **Cost calculator** | Real-time USD cost estimates for GPT-4o, Claude, Gemini and more |
| ⊞ **Compare All Modes** | Runs all four modes simultaneously — side-by-side output |
| 🔴 **Diff View** | Word-level diff showing exactly what was removed before you send |
| 🛡️ **Code & JSON detection** | Auto-detects technical content — destructive transforms disabled automatically |
| 💾 **Persistent preferences** | Remembers your last mode and domain settings via localStorage |
| 📱 **Responsive** | Works on desktop, tablet, and mobile |

---

## Live Demo

> Open `token-optimiser.html` in any browser. No server required.

Or try it with this example — a verbose SOC alert triage prompt:

**Input (237 tokens):**
```
So basically, in order to really understand the full scope of what we are
dealing with here, I wanted to ask you to help me investigate this security
alert that has been triggered in our Elastic SIEM environment...
```

**Structure mode output (~101 tokens, 57% reduction):**
```
[ALERT]
Platform : Elastic SIEM
Type     : Lateral Movement
Source   : 10.4.22.31
Target   : 192.168.1.10
Events   : 847 failed login events within a 60-second window
Note     : Source IP not previously seen in environment
Note     : Target identified as domain controller

[ASK]
• What likely root cause?
• What steps SOC team take to triage investigate alert?
• Whether potentially false positive?
• What containment actions if genuine threat?

[PRIORITY] Need to act quickly — possible active intrusion.
```

---

## Quick Start

```bash
# Clone the repo
git clone https://github.com/YOUR-USERNAME/token-optimiser.git

# Open in browser — that's it
open token-optimiser.html
```

No `npm install`. No build step. No dependencies. Single file.

---

## How It Works

Token Optimiser is **100% deterministic** — no AI, no randomness, no network calls. Everything runs locally in your browser via pure JavaScript.

### Compression Pipeline

```
Your verbose prompt
        │
        ▼
┌─────────────────────────────────────┐
│  1. Whitespace normalisation        │
│  2. Code block protection           │  ← fenced ``` and JSON never touched
│  3. Phrase compression              │  ← "due to the fact that" → "because"
│  4. Filler phrase removal           │  ← "basically", "kind of", "moving forward"
│  5. Stopword removal (Medium+)      │  ← protected prepositions preserved
│  6. Duplicate sentence removal      │  ← normalised comparison
│  7. Adj/adv stripping (Aggressive)  │  ← intensifiers removed
│  8. Bullet conversion (Aggressive)  │  ← long paragraphs → bullet points
│  9. Post-processing cleanup         │  ← dangling punctuation, orphan fragments
└─────────────────────────────────────┘
        │
        ▼
Compressed prompt → send to your paid AI
```

### Structure Mode Pipeline (Security/SIEM)

```
Verbose prose
        │
        ▼
Entity protection (IPs, ports, version numbers tokenised)
        │
        ▼
Sentence splitting (on protected text — IPs never split mid-sentence)
        │
        ▼
Classification: FACT | QUESTION | URGENCY | CONTEXT
        │
        ▼
Security fact extraction (product, alert type, IPs, event count)
        │
        ▼
[ALERT] block + [ASK] block + [PRIORITY] line
        │
        ▼
Structured, labelled prompt output
```

---

## Real Token Numbers

Tested on a realistic verbose SOC analyst SIEM alert prompt (1,251 chars):

| Mode | Chars | Tokens | Reduction | Saved |
|---|---|---|---|---|
| Raw verbose | 1,251 | ~237 | Baseline | — |
| 💡 Light | ~820 | ~155 | ~35% | ~82 tokens |
| ⚙️ Medium | 345 | ~62 | **74%** | 175 tokens |
| 🔥 Aggressive | 176 | ~31 | **87%** | 206 tokens |
| ⚡ Structure | ~380 | ~101 | **57%** | ~136 tokens |

> Token estimates use a dual heuristic: 1 token ≈ 4 chars OR 1 token ≈ 0.75 words (averaged). Actual costs vary by model and tokeniser.

---

## Cost at Scale

With Medium mode on a 10-analyst SOC team running 200 queries/day:

| Metric | Value |
|---|---|
| Tokens wasted daily (per analyst) | 35,000 |
| Tokens wasted monthly (per analyst) | 1,050,000 |
| Tokens saved monthly (10 analysts) | **10.5 million** |

---

## Structure Mode — Domain Profiles

The ⚡ Structure mode supports five domain profiles, each producing domain-appropriate labelled sections:

| Domain | Labels produced |
|---|---|
| 🔒 Security / SIEM | `[ALERT]` `[ASK]` `[PRIORITY]` |
| 🌐 General | `[CONTEXT]` `[REQUEST]` `[PRIORITY]` |
| ⚙️ DevOps / Engineering | `[ENVIRONMENT]` `[ISSUE]` `[PRIORITY]` |
| 💼 Business / Management | `[BACKGROUND]` `[OBJECTIVE]` `[PRIORITY]` |
| ⚖️ Legal / Compliance | `[MATTER]` `[QUESTION]` `[PRIORITY]` |

### Security Domain — Named Entity Protection

The following are automatically detected and protected from stripping:

- **IP addresses** — `10.4.22.31`, `192.168.1.10/24`
- **Version numbers** — `v9.3.1`, `3.2.0`
- **Port references** — `:443`, `:8080`
- **Large numbers** — event counts, timestamps, metrics

### Supported Security Products (auto-detected)

Elastic SIEM, Elastic Security, Splunk, IBM QRadar, Microsoft Sentinel, CrowdStrike, Darktrace, Exabeam, LogRhythm, Palo Alto Networks, Fortinet, Cisco ASA, Check Point, SonicWall, ArcSight, Sumo Logic

### Supported Alert Types (auto-detected)

Lateral movement, brute force, privilege escalation, data exfiltration, ransomware, phishing, malware infection, DDoS, port scan, credential stuffing, SQL injection, command injection, insider threat, anomalous login, authentication failure, account compromise

---

## Supported Models (Cost Calculator)

| Model | Input price used |
|---|---|
| GPT-4o | $2.50 / 1M tokens |
| GPT-4 Turbo | $10.00 / 1M tokens |
| Claude Sonnet | $3.00 / 1M tokens |
| Claude Haiku | $0.80 / 1M tokens |
| Gemini 1.5 Pro | $1.25 / 1M tokens |
| Gemini Flash | $0.075 / 1M tokens |

> Pricing as of Q1 2026. Check your provider's current pricing for exact figures.

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl + Enter` / `Cmd + Enter` | Run optimisation |
| `⚡ Load Example` button | Load the SIEM demo prompt |
| `⊞ Compare All Modes` button | Run all 4 modes simultaneously |

---

## File Structure

```
token-optimiser/
│
└── token-optimiser.html    ← entire application (HTML + CSS + JS, self-contained)
```

That's it. One file.

---

## Safety & Privacy

- **No data leaves your browser.** Your prompts are never sent anywhere.
- **No telemetry.** No analytics. No tracking.
- **No external dependencies at runtime.** The only external resource is the Google Fonts CDN for typography — the app functions fully without it.
- **No localStorage sensitive data.** Only your last-used mode and domain preference are stored.

---

## Use Cases

- **Security operations** — Compress SIEM alert triage prompts before sending to AI assistants
- **DevOps / SRE** — Strip verbose incident descriptions before AI root cause analysis
- **Business analysts** — Reduce lengthy report summaries before AI processing
- **Developers** — Pre-process long code review or debugging prompts
- **Anyone using AI APIs at scale** — Cut your monthly token bill without changing your AI provider

---

## Limitations

Token Optimiser is rule-based, not semantic. It does not understand meaning — it applies deterministic pattern matching. This means:

- **Aggressive mode may occasionally over-compress** — always review the Diff View before sending
- **Structure mode works best on prose** — highly structured input (tables, code) should use Light mode
- **Non-English text** — stopword lists are English-only; use Light mode for other languages
- **Token estimates are approximate** — exact counts depend on your model's tokeniser (tiktoken, etc.)

---


## Related Blog Post

📖 **Stop Chatting with AI Like It's WhatsApp — Every Word Costs Money**

The full blog post explaining the mindset shift behind this tool, with real token numbers, cost analysis, and a walkthrough of all four compression modes.

→ *[Link to your published blog post]*

---

## License

MIT License — free to use, modify, and distribute.

---

*Token Optimiser · by Linkan Dash · No LLM · No API · 100% browser-local*
