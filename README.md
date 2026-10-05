# TrainLens AutoResearch

**Evidence-driven AutoResearch for ML coding agents, powered by TrainLens.**

TrainLens AutoResearch is a small, provider-neutral Skill that teaches coding agents such as Codex and Claude Code how to run controlled ML research loops:

```text
inspect project / notebook
        ↓
TrainLens evidence
        ↓
state uncertainty
        ↓
form hypothesis
        ↓
run cheapest informative experiment
        ↓
TrainLens evidence
        ↓
keep / revert / investigate
        ↺
```

It is intentionally **not** an AutoML framework, agent framework, tracker, scheduler, or hyperparameter-search system. The agent already provides the reasoning and execution runtime. [TrainLens](https://github.com/edujbarrios/trainlens) provides the evidence layer.

## Designed for modern coding agents

This repository intentionally keeps the Skill and agent instructions small.

Modern coding agents can already inspect a repository, understand training code, edit files, run commands, work with notebooks, and reason over results. The Skill therefore defines the **research policy and guardrails**, not a large layer of scaffolding around the agent.

The design principle is:

> **Minimal policy, maximum agent autonomy. TrainLens provides evidence; the host agent provides reasoning.**

That also means Agent Mode does not require TrainLens to configure another LLM. Claude, Codex, Cursor, or another host agent can use the model/provider it already runs on. If you are not using Agent Mode, TrainLens can still call an OpenAI-compatible provider directly to generate LLM reports, diagnoses, and improvement ideas.

## What is parameterized

There is no required YAML configuration. Tell the agent what matters in natural language:

```text
Use TrainLens AutoResearch.

Goal: improve validation F1.
Budget: at most 8 experiments.
Constraints: do not change the dataset; do not tune on the test split.
Scope: model, optimizer, scheduler, and augmentation may be changed.
Success: +1.0 validation F1 point without >10% inference latency regression.
```

The Skill treats these as optional parameters:

- **goal** — what you want to improve or understand
- **budget** — experiment count, wall time, compute, or cost
- **constraints** — things that must remain true
- **editable scope** — what the agent may change
- **success criteria** — what counts as useful progress

If some are omitted, the agent should infer conservative defaults from the project and only ask when missing information materially changes the experiment.

## Install

Install TrainLens in the ML project environment:

```bash
pip install trainlens
```

### Codex

For a repo-scoped Codex Skill, copy `SKILL.md` to:

```text
.codex/skills/trainlens-autoresearch/SKILL.md
```

Then ask Codex to use `trainlens-autoresearch` for the task.

### Claude Code

Copy `SKILL.md` into the target project and import it from that project's `CLAUDE.md`:

```markdown
@path/to/trainlens-autoresearch/SKILL.md
```

This repository's own `CLAUDE.md` already imports the Skill.

## Notebooks are first-class

The Skill is designed to work with existing `.ipynb` workflows, not force them into a new framework.

The agent should inspect the notebook before creating replacement scripts, preserve the user's workflow, and execute the notebook with the available Jupyter/kernel tooling when useful. TrainLens can be used from notebook state with its Python API, while portable runs can be consumed from the terminal.

For example:

```python
from trainlens import build_agent_context

context = build_agent_context(globals())
print(context.to_markdown())
```

Or from a portable TrainLens run:

```bash
trainlens agent-context run.json --format json
```

The agent reasons over that evidence using its **own** model/provider connection.

## Research policy

The Skill follows a simple loop:

**Observe → identify uncertainty → hypothesize → design one informative experiment → run → measure → conclude → repeat.**

A few defaults matter:

- do not tune against held-out test evidence
- prefer one conceptual change per experiment
- do not default to broad hyperparameter search
- distinguish evidence from hypotheses
- prefer cheap experiments that resolve uncertainty
- account for run-to-run variability before celebrating tiny gains
- stop when the goal or budget is reached, or when another experiment is not justified by evidence

See [`SKILL.md`](SKILL.md) for the complete agent workflow.

## Try TrainLens first

If you have not used TrainLens yet, start with the end-to-end notebook:

**[Open the TrainLens quickstart in Colab](https://colab.research.google.com/github/edujbarrios/trainlens/blob/main/examples/quickstart.ipynb)**

Then use this Skill on your own ML project or notebook.

## Repository structure

```text
trainlens-autoresearch/
├── README.md
├── SKILL.md
├── AGENTS.md
├── CLAUDE.md
└── LICENSE
```

That is intentional. Add infrastructure only when the research workflow cannot be expressed reliably with the Skill plus the tools the coding agent already has.
