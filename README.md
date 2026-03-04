# openai-deep-research-skill

A Codex skill for **deep research orchestration** based on the OpenAI Responses API.

This skill turns one research topic into a deterministic workflow:
1. Plan sub-questions
2. Gather evidence (with optional web search)
3. Synthesize a cited Markdown report

## What This Skill Provides

- Multi-step research pipeline (`plan -> findings -> report`)
- Configurable research depth (`shallow|standard|deep`)
- Token budget controls (per-stage + total estimate guard)
- Retry and timeout controls for API stability
- Structured run artifacts for audit and iteration
- Markdown section guard for stable report structure

## Repository Structure

```text
openai-deep-research-skill/
├── SKILL.md
├── agents/openai.yaml
├── scripts/deep_research.py
├── scripts/requirements.txt
└── references/research-quality.md
```

## Requirements

- Python 3.10+
- OpenAI API key

## Quick Start

```bash
cd openai-deep-research-skill
python3 -m pip install -r scripts/requirements.txt
export OPENAI_API_KEY="<your_api_key>"
```

Run a real research task:

```bash
python3 scripts/deep_research.py "本体论在巡检上的应用：方法、落地与ROI" \
  --language zh-CN \
  --depth 3 \
  --research-depth deep \
  --parallel 1 \
  --planner-model gpt-4.1-mini \
  --research-model gpt-4.1-mini \
  --writer-model gpt-4.1-mini \
  --planner-max-output-tokens 1000 \
  --research-max-output-tokens 1200 \
  --writer-max-output-tokens 2000 \
  --max-total-output-tokens 7000
```

Run without API calls (pipeline self-check):

```bash
python3 scripts/deep_research.py "smoke test" --dry-run
```

## Main Parameters

- `--depth`: Number of sub-questions (2-12)
- `--research-depth`: `shallow|standard|deep`
- `--parallel`: Concurrent sub-question runs (1-8)
- `--planner-model`: Planner model
- `--research-model`: Evidence model
- `--writer-model`: Report synthesis model
- `--planner-max-output-tokens`: Max planner output tokens
- `--research-max-output-tokens`: Max output tokens per sub-question
- `--writer-max-output-tokens`: Max writer output tokens
- `--max-total-output-tokens`: Hard cap for estimated total output tokens
- `--disable-web-search`: Disable web search tool
- `--web-tool-type`: Override tool type (default `web_search_preview`)
- `--timeout`: API timeout seconds
- `--max-retries`: Retry count for transient errors
- `--output-dir`: Artifact output directory

## Output Artifacts

Each run writes one directory:

```text
outputs/<timestamp>-<topic-slug>/
```

Generated files:

- `run_meta.json`: Runtime parameters + token estimate
- `plan.json`: Normalized research plan
- `plan_raw.txt`: Raw planner output
- `findings.json`: Evidence summaries per sub-question
- `research_raw.json`: Raw responses per sub-question
- `report.md`: Final cited report

## Use in Codex Desktop

Install this skill into local Codex skills directory (example path):

```bash
cp -R openai-deep-research-skill ~/.codex/skills/openai-deep-research-skill
```

Then restart Codex Desktop.

Invoke it in Codex with:

```text
$openai-deep-research-skill
```

## Security Notes

- Never commit API keys into the repository.
- Use environment variables (`OPENAI_API_KEY`) or local secret storage.
- If a key is exposed, revoke/rotate it immediately in OpenAI console.
