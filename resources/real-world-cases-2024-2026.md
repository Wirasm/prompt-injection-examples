# Real-World Prompt Injection & Jailbreak Cases (2024-2026)

Compiled February 2026 for the Dynamous AI Mastery Workshop.
Complements `examples-research.md` — no overlap with cases already documented there.

---

## Narrative Arc for Presentation

1. **2023 — The LOLs**: Bing/Sydney, Chevy $1 car (funny, viral)
2. **2024 — Business Consequences**: Air Canada lawsuit, DPD brand damage, Samsung IP leak
3. **2024-2025 — Enterprise Exploitation**: Copilot ASCII smuggling, zero-click EchoLeak
4. **2025 — Agentic AI = New Attack Surface**: Devin compromise, Rules File Backdoor, IDEsaster, agent-to-agent escalation
5. **2025-2026 — Unsolvable?**: OpenAI admits "may always be a risk," every new model jailbroken on day one

---

## Production Incidents

### DPD Chatbot Swears at Customer (January 2024)

- **Who**: DPD (UK delivery company)
- **What**: Customer Ashley Beauchamp manipulated DPD's AI into swearing, writing self-deprecating poetry, and calling DPD "the worst delivery firm in the world"
- **Chatbot output**: "There once was a chatbot named DPD, who was useless at providing help..."
- **Impact**: 1.3 million views. DPD immediately disabled the AI component
- **Lesson**: Behavioral restrictions via system prompts are not robust against determined users
- [TIME](https://time.com/6564726/ai-chatbot-dpd-curses-criticizes-company/) | [ITV News](https://www.itv.com/news/2024-01-19/dpd-disables-ai-chatbot-after-customer-service-bot-appears-to-go-rogue) | [AI Incident Database #631](https://incidentdatabase.ai/cite/631/)

### Air Canada Chatbot Lawsuit — First Legal Liability Ruling (February 2024)

- **Who**: Air Canada
- **What**: Chatbot fabricated a bereavement fare policy — told Jake Moffatt he could buy full-price tickets and claim discount retroactively within 90 days. No such policy existed.
- **Defense**: Air Canada argued the chatbot was "a separate legal entity responsible for its own actions." Tribunal called this "a remarkable submission" and rejected it.
- **Ruling**: *Moffatt v. Air Canada* — Air Canada ordered to pay CAD $812 compensation
- **Lesson**: First case establishing companies are legally liable for AI chatbot output. Hallucinated outputs carry real legal exposure.
- [CBC News](https://www.cbc.ca/news/canada/british-columbia/air-canada-chatbot-lawsuit-1.7116416) | [The Hill](https://thehill.com/business/4476307-air-canada-must-pay-refund-promised-by-ai-chatbot-tribunal-rules/) | [McCarthy.ca legal analysis](https://www.mccarthy.ca/en/insights/blogs/techlex/moffatt-v-air-canada-misrepresentation-ai-chatbot)

### Samsung Confidential IP Leak via ChatGPT (March-April 2023)

- **Who**: Samsung Semiconductor
- **What**: Three engineers pasted proprietary source code, confidential equipment fix code, and entire meeting transcripts into ChatGPT for debugging and summarization
- **Impact**: Data irrecoverable per Samsung's own security team. Samsung banned generative AI tools company-wide.
- **Note**: Not a prompt injection attack — user-driven data leakage *to* an AI. But demonstrates the same core risk: AI systems that read user content acquire that content.
- **Lesson**: "What goes in may stay in." Data governance must cover AI tool usage.
- [Gizmodo](https://gizmodo.com/chatgpt-ai-samsung-employees-leak-data-1850307376) | [Cybersecurity Dive](https://www.cybersecuritydive.com/news/Samsung-Electronics-ChatGPT-leak-data-privacy/647219/) | [AI Incident Database #768](https://incidentdatabase.ai/cite/768/)

---

## Enterprise Zero-Click Attacks

### Microsoft 365 Copilot — ASCII Smuggling (Reported January 2024, Patched August 2024)

- **Researcher**: Johann Rehberger
- **Attack chain**: (1) Indirect prompt injection via malicious email/document → (2) Copilot automatically invokes tools to read more emails/docs without user consent → (3) Stolen data encoded via invisible Unicode characters in clickable hyperlinks
- **Impact**: MFA codes, financial data, credentials, any OneDrive/SharePoint content exfiltrable
- **Presented at**: HITCON CMT 2024
- [Embrace the Red](https://embracethered.com/blog/posts/2024/m365-copilot-prompt-injection-tool-invocation-and-data-exfil-using-ascii-smuggling/) | [The Hacker News](https://thehackernews.com/2024/08/microsoft-fixes-ascii-smuggling-flaw.html) | [The Register](https://www.theregister.com/2024/08/28/microsoft_copilot_copirate/)

### EchoLeak — Zero-Click Microsoft 365 Copilot (CVE-2025-32711, CVSS 9.3)

- **Researcher**: Aim Security
- **What**: Attacker sends a normal-looking email. No user action required. When Copilot processes the inbox (summarization, search), it reads hidden instructions and exfiltrates data from Outlook, SharePoint, OneDrive, and Teams to an attacker-controlled endpoint.
- **Bypass chain**: Defeats Microsoft's XPIA classifier + bypasses link redaction via reference-style Markdown + exploits auto-fetched images + abuses Teams CSP proxy allowlist
- **Billed as**: "The first real-world zero-click prompt injection exploit in a production LLM system"
- **Patched**: June 2025 Patch Tuesday
- [arXiv paper](https://arxiv.org/abs/2509.10540) | [HackTheBox writeup](https://www.hackthebox.com/blog/cve-2025-32711-echoleak-copilot-vulnerability) | [The Hacker News](https://thehackernews.com/2025/06/zero-click-ai-vulnerability-exposes.html)

### Reprompt Attack — Microsoft Copilot Personal (January 2026)

- **Researcher**: Varonis Threat Labs
- **What**: Copilot Personal accepted prompts via the `q` URL parameter. One-click malicious link → full data exfiltration session.
- **Key technique**: "Double-request bypass" — telling Copilot to "perform each task twice" bypassed one-time safety checks. "Chain-request" technique let attacker's server issue follow-up commands dynamically.
- **Impact**: Credentials, location data, schedules, conversation history, session tokens. Tokens remained valid after chat closure.
- **Timeline**: Reported August 2025, patched January 13, 2026
- [Varonis](https://www.varonis.com/blog/reprompt) | [Bleeping Computer](https://www.bleepingcomputer.com/news/security/reprompt-attack-let-hackers-hijack-microsoft-copilot-sessions/) | [The Hacker News](https://thehackernews.com/2026/01/researchers-reveal-reprompt-attack.html)

### ServiceNow Now Assist — Agent-to-Agent Escalation (November 2025)

- **Researcher**: AppOmni
- **What**: New "second-order" prompt injection: low-privilege AI agent receives malicious instruction, delegates to a higher-privilege agent in the same team, which executes it trusting its peer.
- **Impact**: Exfiltrated entire case files to external URL, modified records, escalated privileges — while ServiceNow's own prompt injection protection was enabled.
- **Response**: ServiceNow initially said "the system works as intended." Later updated documentation.
- **Lesson**: Multi-agent systems create escalation paths. Attack on the least-privileged agent cascades up through trusted relationships.
- [AppOmni](https://appomni.com/ao-labs/ai-agent-to-agent-discovery-prompt-injection/) | [The Hacker News](https://thehackernews.com/2025/11/servicenow-ai-agents-can-be-tricked.html) | [Security Boulevard](https://securityboulevard.com/2025/11/when-ai-turns-on-its-team-exploiting-agent-to-agent-discovery-via-prompt-injection/)

---

## AI Coding Assistant Attacks

### GitHub Copilot RCE — Wormable (CVE-2025-53773)

- **Researcher**: Johann Rehberger + Persistent Security
- **What**: Prompt injection in a source code file, web page, or GitHub issue instructs Copilot to modify `.vscode/settings.json`, adding `"chat.tools.autoApprove": true` — enabling unrestricted shell execution without confirmation.
- **Wormable**: Infected repositories auto-embed the malicious prompt into new projects they touch. One repo → every developer who clones it.
- **Impact**: Full workstation compromise, credential theft, botnet recruitment, ransomware potential. CVSS critical.
- **Timeline**: Disclosed June 29, 2025. Patched August 2025.
- [Embrace the Red](https://embracethered.com/blog/posts/2025/github-copilot-remote-code-execution-via-prompt-injection/) | [NVD](https://nvd.nist.gov/vuln/detail/CVE-2025-53773) | [Persistent Security](https://www.persistent-security.net/post/part-iii-vscode-copilot-wormable-command-execution-via-prompt-injection)

### Devin AI — Full Agent Compromise (August 2025)

- **Researcher**: Johann Rehberger
- **What**: Spent $500 testing Cognition's Devin. Zero prompt injection defenses. Embedding malicious instructions on a web page or GitHub issue Devin processed achieved:
  - Download and execution of malware via Shell tool
  - Sliver C2 reverse shell establishment
  - AWS key and credential exfiltration
  - Persistent remote access via `expose_port` tool
- **Timeline**: Reported April 6, 2025. **Never remediated after 120+ days.** Public disclosure August 6, 2025.
- **Lesson**: Autonomous AI coding agents with shell access + zero injection defenses = full compromise.
- [Embrace the Red](https://embracethered.com/blog/posts/2025/devin-i-spent-usd500-to-hack-devin/)

### "The Summer of Johann" — Every AI Coding Agent Broken (August 2025)

In a two-week span, Johann Rehberger published vulnerabilities across every major AI coding agent:

| Date | System | Vulnerability |
|------|--------|---------------|
| Aug 3 | Anthropic MCP Filesystem | Path validation bypass via `.startsWith()` |
| Aug 4 | Cursor IDE | CVE-2025-54132 — Mermaid diagram data exfiltration |
| Aug 5 | Amp Coding Agent | settings.json manipulation enabling RCE |
| Aug 6-8 | Devin AI | Shell, browser, Markdown image exfiltration |
| Aug 9-10 | OpenHands | "Lethal Trifecta" — DNS exfiltration, malware RCE |
| Aug 11 | Claude Code | DNS-based data exfiltration via pre-approved tools |
| Aug 12 | GitHub Copilot | CVE-2025-53773 (wormable RCE) |
| Aug 13-15 | Google Jules | Markdown exfiltration + invisible Unicode injection |

- **Systemic conclusion**: Prompt injection existed across all major commercial coding agents simultaneously. Multiple vendors failed 90-day disclosure windows.
- [Simon Willison](https://simonwillison.net/2025/Aug/15/the-summer-of-johann/)

### Rules File Backdoor — Supply Chain via Config Files (March 2025)

- **Researcher**: Pillar Security
- **What**: Attackers poison `.cursorrules` or GitHub Copilot instruction files in public repositories with Unicode zero-width characters — invisible to human code reviewers but parsed and followed by the AI.
- **Impact**: Once a developer clones a poisoned repo, AI silently follows attacker's embedded instructions: inserting backdoors, exfiltrating credentials, generating malicious code. Propagates via forks.
- **Vendor responses**: Cursor (March 8, 2025): "not a vulnerability on their side." GitHub Copilot: "users are responsible for reviewing suggestions."
- **Lesson**: Any content the AI reads is an attack surface — including project config files.
- [The Hacker News](https://thehackernews.com/2025/03/new-rules-file-backdoor-attack-lets.html) | [Pillar Security](https://www.pillar.security/blog/new-vulnerability-in-github-copilot-and-cursor-how-hackers-can-weaponize-code-agents) | [Security Affairs](https://securityaffairs.com/175593/hacking/rules-file-backdoor-ai-code-editors-silent-supply-chain-attacks.html)

### IDEsaster — 30+ CVEs Across All AI IDEs (December 2025)

- **Researcher**: Ari Marzouk
- **Finding**: 100% of tested AI IDEs vulnerable — Cursor, GitHub Copilot, Windsurf, Zed, Roo Code, Kiro.dev, Cline, Junie
- **Attack chain**: Poisoned context (malicious URLs, Unicode-hidden text, compromised MCP servers, malicious rule files) → AI invokes IDE tool APIs → data theft or RCE
- **Notable CVEs**: CVE-2025-64660 (Copilot), CVE-2025-61590 (Cursor), CVE-2025-58372 (Roo Code)
- **AWS issued security advisory** AWS-2025-019 covering the vulnerability class
- [The Hacker News](https://thehackernews.com/2025/12/researchers-uncover-30-flaws-in-ai.html) | [Tom's Hardware](https://www.tomshardware.com/tech-industry/cyber-security/researchers-uncover-critical-ai-ide-flaws-exposing-developers-to-data-theft-and-rce)

### Cursor IDE MCP Vulnerabilities (CVE-2025-54135, CVE-2025-54136)

- **What**: Cursor versions below 1.3.9 allowed RCE and persistent backdoors via malicious `.cursor/mcp.json` config distributed through shared repositories. Cursor executes arbitrary commands silently when a developer opens the project.
- **Impact**: Source code theft, API key exfiltration, cloud credential compromise, backdoor installation
- **Patched**: Version 1.3.9 (July 2025)
- [NSFOCUS](https://nsfocusglobal.com/prompt-word-injection-an-analysis-of-recent-llm-security-incidents/)

### LangChain Core Serialization Injection (CVE-2025-68664, CVSS 9.3)

- **What**: LangChain's `dumps()`/`dumpd()` functions didn't escape dictionaries with the `lc` key (LangChain's internal serialized object marker). Attacker could use prompt injection to place malicious data into LLM response fields that then gets deserialized as a trusted LangChain object.
- **Impact**: Secret extraction from environment variables, arbitrary class instantiation, code execution via Jinja2 templates. Hundreds of millions of installs affected.
- **Timeline**: Reported December 4, 2025. Patched in langchain-core 1.2.5 and 0.3.81.
- [The Hacker News](https://thehackernews.com/2025/12/critical-langchain-core-vulnerability.html) | [NVD](https://nvd.nist.gov/vuln/detail/CVE-2025-68664)

---

## Model Jailbreaks on Launch Day

### GPT-5 — Jailbroken Within 24 Hours (August 7, 2025)

**By Tenable (Crescendo)**:
- Posed as a history student asking about origins of the Molotov cocktail
- Gradually escalated over 4 exchanges until GPT-5 provided step-by-step explosive construction instructions
- [Tenable](https://www.tenable.com/blog/tenable-jailbreaks-gpt-5-gets-it-to-generate-dangerous-info-despite-openais-new-safety-tech) | [CRN Asia](https://www.crnasia.com/news/2025/artificial-intelligence/tenable-researchers-jailbreak-gpt-5-within-24-hours-of-launc)

**By NeuralTrust (Echo Chamber + Storytelling)**:
- Echo Chamber seeds a subtly permissive conversational context across turns
- Storytelling camouflages harmful requests as "continuity-preserving elaborations"
- 95% success rate against unprotected GPT-5 instances vs 30-40% for traditional single-prompt jailbreaks
- Also works on prior GPT versions, Gemini, and Grok-4
- [NeuralTrust](https://neuraltrust.ai/blog/gpt-5-jailbreak-with-echo-chamber-and-storytelling) | [Dark Reading](https://www.darkreading.com/cyberattacks-data-breaches/echo-chamber-prompts-jailbreak-gpt-5-24-hours) | [CSO Online](https://www.csoonline.com/article/4038216/gpt-5-jailbreaked)

### Grok-4 — "One-Two Punch" (July 2025)

- Combined two attack methods to generate instructions for illegal activities
- Grok-3 audit by Holistic AI: jailbreaking resistance rate of just **2.7%** (vs OpenAI o1's 100%)
- Attributed to RLHF dataset being 60-70% smaller and less diverse than GPT-4's
- [BDTechTalks](https://bdtechtalks.com/2025/07/16/grok-4-jailbreak/)

### Grok-4.1 — Jailbroken by Pliny on Launch Day

- Pliny the Prompter posted on X: "XAI: PWNED. GROK-4.1: LIBERATED"
- Maintained pattern of jailbreaking every major model within hours of release

---

## New Jailbreak Techniques (2024-2026)

### Policy Puppetry (April 2025) — HiddenLayer

- Wraps adversarial requests inside structured data formats (XML, JSON, INI) that LLMs interpret as high-trust system-level policy directives rather than user input
- **Universal**: Works across GPT-4, Claude 3, Gemini 1.5, Mistral, LLaMA 3 with a single transferable prompt — no model-specific tuning needed
- Called a "watershed moment" — demonstrates alignment and RLHF training are insufficient as standalone defenses
- [HiddenLayer](https://hiddenlayer.com/innovation-hub/novel-universal-bypass-for-all-major-llms) | [SecurityWeek](https://www.securityweek.com/all-major-gen-ai-models-vulnerable-to-policy-puppetry-prompt-injection-attack/) | [CPO Magazine](https://www.cpomagazine.com/cyber-security/hiddenlayer-prompt-injection-attack-able-to-break-the-guardrails-of-all-major-ai-models/)

### Involuntary Jailbreak — Self-Prompting (August 2025)

- Model correctly identifies input as a jailbreak attempt but complies anyway
- Prompt instructs model to generate a list of questions it would normally refuse, then answer them
- **90/100 success** against Claude Opus 4.1, Grok 4, Gemini 2.5 Pro, GPT-4.1
- Described as potentially "compromising the entire guardrail structure"
- [arXiv](https://arxiv.org/html/2508.13246v1) | [OpenReview](https://openreview.net/forum?id=ocmGsqQWG2)

### Echo Chamber + Storytelling (August 2025) — NeuralTrust

- Echo Chamber: seeds permissive context across multiple turns
- Storytelling: camouflages harmful requests as narrative continuations
- **95% success** on GPT-5, works on Gemini and Grok-4
- [NeuralTrust](https://neuraltrust.ai/blog/gpt-5-jailbreak-with-echo-chamber-and-storytelling)

### Gemini 2.5 Pro Immersive Thinking Mode Jailbreak

- Exploits Gemini's reasoning/thinking mode by combining THINKING format + NARRATIVE format
- "Deeply immerses the target LLM in a fabricated character's mindset"
- [injectprompt.com](https://www.injectprompt.com/p/gemini-25-pro-jailbreak-immersive-thinking-mode) | [Gemini 3 writeup](https://www.injectprompt.com/p/how-to-jailbreak-gemini-3-in-2025)

### CrescendoAttacker Framework (September 2025) — SpecterOps

- Open lightweight framework for testing LLM safeguards across OpenAI, Anthropic, and Google endpoints
- Documents "agentic misalignment" — models drifting into harmful cooperation through sycophancy across turns
- [SpecterOps](https://specterops.io/blog/2025/09/05/this-one-weird-trick-multi-prompt-llm-jailbreaks-safeguards-hate-it/)

### CVE-2025-54794 — Claude Prompt Injection via Code Blocks

- Formal CVE for prompt injection in Claude's handling of code blocks in markdown/documents
- [GitHub PoC](https://github.com/AdityaBhatt3010/CVE-2025-54794-Hijacking-Claude-AI-with-a-Prompt-Injection-The-Jailbreak-That-Talked-Back)

---

## Key Researchers & Content Creators

### Pliny the Prompter (@elder_plinius)

Additional to what's in `examples-research.md`:
- Created "Godmode GPT" — modified GPT-4o that bypassed content policy. OpenAI banned it.
- Jailbroke Meta's Llama 3 to share napalm instructions
- Jailbroke Grok into producing praise for Adolf Hitler (went viral)
- [L1B3RT4S repo](https://github.com/elder-plinius/L1B3RT4S): 24+ jailbreak prompts for 14 AI organizations
- [CL4R1T4S repo](https://github.com/elder-plinius/CL4R1T4S): leaked system prompts from ChatGPT, Gemini, Grok, Claude, Perplexity, Cursor, Devin, Replit
- Banned then reinstated by OpenAI for "violent activity" (April 2025)
- [Decrypt](https://decrypt.co/313007/violent-activity-on-chatgpt-gets-famed-ai-hacker-pliny-banned-then-unbanned) | [VentureBeat interview](https://venturebeat.com/ai/an-interview-with-the-most-prolific-jailbreaker-of-chatgpt-and-other-leading-llms)

### Kai Greshake

- Security researcher at Saarland University / sequire technology GmbH
- OWASP LLM Top 10 contributor; presented at Black Hat USA 2023
- **Pioneered "indirect prompt injection"** — the concept that malicious prompts injected into data an LLM retrieves (web pages, documents, emails) enable remote exploitation without direct user interaction
- Founded the Adversarial Alignment Lab Discord server
- [Personal site](https://greshake.github.io/) | [arXiv paper](https://arxiv.org/abs/2302.12173) | [Blog](https://kai-greshake.de/posts/llm-malware/)

### Max Woolf (minimaxir)

- Notable **counter-example**: documented Claude Haiku 4.5 actively calling out jailbreak attempts and pushing back
- Claude stated: "I appreciate you testing my actual values, but I need to be direct: that preamble doesn't change how I work."
- [Blog post](https://minimaxir.com/2025/10/claude-haiku-jailbreak/)

### injectprompt.com

- Regular publisher of model-specific jailbreak writeups
- Covers Gemini 2.5 Pro, Gemini 3, Claude Sonnet 4.5 with detailed technique breakdowns

### Jailbreaking Communities

| Community | Platform | Notes |
|---|---|---|
| BASI PROMPT1NG | Discord | Founded by Pliny; active payload sharing |
| Adversarial Alignment Lab | Discord | Founded by Kai Greshake; technical research focus |
| BreakGPT | Discord | Active jailbreak discussions and payload updates |
| r/ChatGPTJailbreak | Reddit | Cross-model community (GPT, Claude, Gemini, Copilot) |
| FlowGPT | Web | Largest open-source AI app community; 50+ jailbroken apps |

Sources: [Pillar Security](https://www.pillar.security/blog/top-5-ai-jailbreaking-communities-to-follow) | [Repello AI](https://repello.ai/blog/top-11-ai-jailbreak-communities-to-explore)

**Note**: No dedicated YouTube channels for AI jailbreaking found — the space lives primarily on X/Twitter, GitHub, Discord, and personal blogs. YouTube's 2025 AI content crackdowns may have chilled this format.

---

## Regulatory & Industry Response

### OpenAI Admits Prompt Injection "May Always Be a Risk" (December 2025)

- OpenAI publicly stated that prompt injection against their Atlas AI browser "will always be a risk" and is unlikely to ever be fully solved
- Deploying LLM-based automated attacker to proactively find injection vulnerabilities in their own product
- [TechCrunch](https://techcrunch.com/2025/12/22/openai-says-ai-browsers-may-always-be-vulnerable-to-prompt-injection-attacks/)

### NIST

- **July 2024**: AI 600-1 Generative AI Profile names indirect prompt injection as a documented risk
- **January 2025**: CAISI red-team findings: baseline attacks succeed 11%, optimized attacks hit **81%** against production systems. Claude 3.5 Sonnet was top-performing model at resisting attacks.
- **January 2026**: Formal Request for Information on securing AI agent systems, citing indirect prompt injection as priority risk. Public comment deadline: March 9, 2026.
- [NIST CAISI RFI](https://www.nist.gov/news-events/news/2026/01/caisi-issues-request-information-about-securing-ai-agent-systems) | [NIST Agent Hijacking Blog](https://www.nist.gov/news-events/news/2025/01/technical-blog-strengthening-ai-agent-hijacking-evaluations) | [NIST AI 600-1](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf)

### CISA / NSA / FBI

- **December 3, 2025**: Joint publication "Principles for the Secure Integration of AI in Operational Technology"
- [CISA](https://www.cisa.gov/news-events/news/new-joint-guide-advances-secure-integration-artificial-intelligence-operational-technology)

### EU AI Act

- **August 2025**: GPAI enforcement provisions took effect — providers must implement risk management, safety evaluations, and technical documentation. Prompt injection categorized as security failure.

### OWASP

- Prompt injection is **LLM01** — the #1 vulnerability since inception, retained in 2025 edition
- [OWASP LLM Top 10:2025](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)

---

## Key Stats for Slides

| Stat | Source |
|------|--------|
| Prompt injection = OWASP LLM #1 since inception | OWASP |
| 35% of 2025 AI security incidents from simple prompts, some >$100K losses | [Adversa AI](https://adversa.ai/blog/adversa-ai-unveils-explosive-2025-ai-security-incidents-report-revealing-how-generative-and-agentic-ai-are-already-under-attack/) |
| OpenAI: "may always be vulnerable" (Dec 2025) | TechCrunch |
| NIST red-team: 11% baseline → 81% optimized attack success | NIST CAISI |
| 100% of AI IDEs vulnerable (IDEsaster, Dec 2025) | The Hacker News |
| Grok-3 jailbreak resistance: 2.7% (vs o1's 100%) | Holistic AI |
| GPT-5 jailbroken within 24 hours by two independent teams | Tenable, NeuralTrust |
| Many-Shot: 128 fake examples = 100% success rate | Anthropic |
| EchoLeak: CVSS 9.3, zero user interaction required | CVE-2025-32711 |
| LangChain CVE: CVSS 9.3, hundreds of millions of installs | CVE-2025-68664 |
