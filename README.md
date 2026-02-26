# Prompt Injection Workshop

Workshop demos and resources for the Dynamous AI Mastery community workshop on prompt injection.

## Setup

```bash
uv sync
export ANTHROPIC_API_KEY=your-key-here
```

## Demos

All demos use Claude (Anthropic SDK) and run as interactive terminal scripts.

| Demo | Section | What it shows |
|---|---|---|
| `demo1_the_hook.py` | The Hook (10 min) | "Secure" chatbot broken by basic injection |
| `demo2_input_filtering.py` | Defense Layer 1 | Regex filter catches obvious attacks, bypassed by creative ones |
| `demo3_prompt_design.py` | Defense Layer 2 | Weak vs. strong prompt architecture compared side-by-side |
| `demo4_rag_poisoning.py` | Real Attack + Layer 3 | 1 poisoned doc in a knowledge base corrupts all responses |
| `demo5_indirect_injection.py` | Real Attack | Hidden instructions in emails, docs, and web pages |

### Running demos

```bash
uv run python demos/demo1_the_hook.py
uv run python demos/demo2_input_filtering.py
uv run python demos/demo3_prompt_design.py
uv run python demos/demo4_rag_poisoning.py
uv run python demos/demo5_indirect_injection.py
```

Each demo is interactive — press Enter to advance, type prompts in interactive mode, type `quit` to exit.

## Resources

- `resources/examples-research.md` — Real-world examples, researcher profiles, attack techniques
- `resources/defense-strategies-research.md` — Defense tools, papers, implementation patterns
