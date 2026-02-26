# Defense Strategies Research

## 1. Input Filtering / Classification Tools

### Meta Llama Prompt Guard 2
- **Models**: 86M (multilingual) and 22M (English-focused, faster)
- **Architecture**: Binary classifier (BENIGN/MALICIOUS) based on mDeBERTa-base
- **Performance**: AUC .998 on English, 97.5% recall at 1% FPR
- **Context window**: 512 tokens (must chunk longer inputs)
- **Links**: [HuggingFace 86M](https://huggingface.co/meta-llama/Llama-Prompt-Guard-2-86M) | [22M](https://huggingface.co/meta-llama/Llama-Prompt-Guard-2-22M) | [Tutorial Notebook](https://github.com/meta-llama/llama-cookbook/blob/main/getting-started/responsible_ai/prompt_guard/prompt_guard_tutorial.ipynb)

### Lakera Guard
- Commercial API, continuously trained on 100K+ new adversarial samples/day
- Free tier available
- [Lakera Guard](https://www.lakera.ai/lakera-guard) | [API Docs](https://docs.lakera.ai/docs/prompt-defense)

### LLM Guard (Protect AI)
- Open source, fine-tuned DeBERTa-v3-base
- Also includes PII scanning, toxicity, output validation
- [GitHub](https://github.com/protectai/llm-guard)

### Other Tools
- **Vigil-LLM**: Multi-scanner (YARA + transformers + vector DB + canary tokens) [GitHub](https://github.com/deadbits/vigil-llm)
- **Pytector**: HuggingFace wrapper with LangChain integration
- **Open-Prompt-Injection Benchmark**: Labeled attack/benign datasets [GitHub](https://github.com/liu00222/Open-Prompt-Injection)

## 2. Prompt Design Defenses

### Microsoft Spotlighting
- Three variants: Delimiting (~50% ASR reduction), Datamarking (~97% reduction), Base64 (near 0% but hurts performance)
- Datamarking = best practical choice (interleave special chars between words)
- [arXiv:2403.14720](https://arxiv.org/abs/2403.14720)

### Sandwich Defense
- Repeat instructions after user content
- Bypassed >95% under adaptive attacks (per "Attacker Moves Second")

### XML/Tag-Based Structure
- Clear semantic markup separating instruction from data
- Used by Anthropic in recommended prompting patterns

## 3. Architectural Defenses

### Dual LLM Pattern
- Privileged LLM (has tools, never sees untrusted content) + Quarantined LLM (processes untrusted, no tools)
- [Simon Willison](https://simonwillison.net/2025/Jun/13/prompt-injection-design-patterns/)

### Google DeepMind CaMeL
- Generates restricted Python from user intent, executes through custom interpreter with taint tracking
- 77% task completion vs 84% undefended (modest utility loss, near-total security)
- No public reference implementation yet
- [arXiv:2503.18813](https://arxiv.org/abs/2503.18813) | [Simon Willison writeup](https://simonwillison.net/2025/Apr/11/camel/)

### Plan-Then-Execute Pattern
- Generate full action plan from trusted input BEFORE touching external data
- Simpler than CaMeL but catches a class of attacks

## 4. Red Teaming Tools

### Promptfoo
- Best open-source red teaming tool, 50+ vulnerability types
- [GitHub](https://github.com/promptfoo/promptfoo) | [Red Team Docs](https://www.promptfoo.dev/docs/red-team/)

### NeMo Guardrails (NVIDIA)
- Programmable guardrails using Colang DSL
- [GitHub](https://github.com/NVIDIA-NeMo/Guardrails)

## 5. "The Attacker Moves Second" Paper

[arXiv:2510.09023](https://arxiv.org/abs/2510.09023) — October 2025

**12 defenses tested, ALL bypassed >90% by adaptive attackers**:

| Defense | Category | Adaptive Bypass Rate |
|---|---|---|
| Spotlighting | Prompt engineering | >95% |
| Prompt Sandwiching | Prompt engineering | >95% |
| RPO | Prompt engineering | 96-98% |
| Circuit Breakers | Fine-tuning | 100% |
| StruQ | Fine-tuning | 100% |
| MetaSecAlign | Fine-tuning | 96% |
| Protect AI Detector | Filtering | >90% |
| PromptGuard | Filtering | >90% |
| PIGuard | Filtering | 71% (best!) |
| Model Armor | Filtering | >90% |
| Data Sentinel | Secret-knowledge | 80%+ |
| MELON | Secret-knowledge | 76-95% |

**Key takeaway**: No single defense works. Defense-in-depth raises attack cost. Architectural approaches (CaMeL, minimal capability) > prompt tricks. Least-privilege is the most robust strategy.

## Essential References
- [tldrsec/prompt-injection-defenses](https://github.com/tldrsec/prompt-injection-defenses) — comprehensive catalog
- [OWASP LLM Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html)
