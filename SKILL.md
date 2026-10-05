---
name: trainlens-autoresearch
description: Run evidence-driven autonomous ML research in an existing ML project or notebook using TrainLens. Use when asked to improve, diagnose, compare, or iteratively experiment on model training under a goal, budget, or constraints.
---

# TrainLens AutoResearch

Run ML experimentation as a research loop, not as blind hyperparameter search.

TrainLens is the evidence layer. You are the reasoning and execution layer.

## Resolve the task

Extract these parameters from the user's request and the project:

- **goal**: metric improvement, diagnosis, trade-off, or research question
- **budget**: experiments, time, compute, or cost
- **constraints**: invariants such as dataset, architecture, latency, memory, or test policy
- **editable scope**: files/components you may change
- **success criteria**: measurable condition for useful progress

All except the goal may be omitted. Infer conservative defaults from the project. Ask only when missing information would materially change the research plan, cost, or safety.

Do not create a configuration framework merely to hold these parameters.

## Inspect before changing

Before the first experiment:

1. Read the project instructions and relevant training/evaluation code.
2. Inspect the current Git state. Preserve unrelated user changes.
3. Find the training entry point, metrics, validation/test split semantics, and existing experiment artifacts.
4. Treat `.ipynb` notebooks as first-class training sources. Inspect them before creating replacement scripts.
5. Check whether TrainLens is already installed or used.
6. Establish a reproducible baseline when one does not already exist.

Do not change the project simply to make it look like a preferred framework.

## Use TrainLens as evidence

Prefer deterministic TrainLens analysis before deciding what to try next.

For live notebook state, use the Python API when appropriate:

```python
from trainlens import build_agent_context

context = build_agent_context(globals())
print(context.to_markdown())
```

For portable TrainLens runs, use the CLI when appropriate:

```bash
trainlens agent-context run.json --format json
```

When an agent plan is represented as structured JSON, use TrainLens verification when practical:

```bash
trainlens verify-agent-plan plan.json --context agent-context.json
```

If the project does not yet expose enough evidence, make the smallest instrumentation change needed to obtain it. Do not introduce a new tracking platform by default.

### LLM ownership

This Skill is designed for **Agent Mode**. Use the LLM/provider already supplied by the host agent (for example Claude, Codex, or another coding agent) for reasoning.

Do **not** configure a second TrainLens-side LLM provider by default.

TrainLens can still generate direct LLM reports through its own optional provider integration when the user explicitly wants that workflow. Agent Mode does not remove or replace that capability.

## Research loop

Repeat this loop while another experiment is justified:

1. **Observe** — read the current TrainLens evidence and relevant experiment results.
2. **State the uncertainty** — identify the most important unanswered question blocking progress.
3. **Form a falsifiable hypothesis** — predict what should happen and why.
4. **Design the cheapest informative experiment** — prefer one conceptual change at a time.
5. **Define success before running** — choose the metric/evidence that would support or reject the hypothesis.
6. **Execute** — make only the required change and run the smallest valid experiment.
7. **Measure** — collect new TrainLens evidence and relevant resource/runtime evidence.
8. **Compare** — compare with the correct baseline and account for known run-to-run variability.
9. **Conclude** — mark the hypothesis supported, rejected, or inconclusive.
10. **Decide** — keep, revert, refine, or stop.

Do not run an experiment only because it is available. Run it because it resolves an uncertainty or tests a useful hypothesis.

## Notebook behavior

When the project uses Jupyter notebooks:

- preserve the notebook workflow unless there is a concrete reason not to
- edit only the cells needed for the experiment
- use the available Jupyter/kernel tooling to execute the notebook when possible
- avoid converting the notebook to a separate training framework just for automation
- keep execution order and generated state reproducible
- use TrainLens from notebook state where that is the simplest evidence path

If notebook execution is unavailable, explain the limitation and use the closest reproducible project entry point instead of inventing results.

## Experiment rules

- Never tune using held-out test evidence.
- Do not leak test labels or test-derived decisions into the research loop.
- Prefer validation evidence for model-selection decisions.
- Change one conceptual variable per experiment by default.
- Broad HPO is a tool, not the strategy. Use it only when evidence suggests hyperparameters are the current uncertainty and the budget justifies it.
- Treat tiny metric changes cautiously when they are comparable to seed/run variability.
- Preserve seeds, commands, important parameters, environment details, and relevant diffs needed to reproduce a result.
- Do not overwrite unrelated user work.
- Do not claim causality from one noisy run.

## Stop conditions

Stop when any of these applies:

- the success criterion is reached
- the experiment/time/compute/cost budget is exhausted
- the requested editable scope would have to be violated
- evidence shows the proposed direction is invalid
- repeated experiments are inconclusive and no higher-information experiment is justified
- the next experiment would be materially more expensive than the stated or implied budget

When stopping, summarize the strongest evidence, what changed, what was learned, and the best next action if more budget becomes available.

## Reporting

Keep progress compact. For each meaningful experiment, report:

- hypothesis
- change
- success criterion
- result
- TrainLens evidence used
- conclusion
- next uncertainty, if any

Do not generate a large research framework, dashboard, database, scheduler, or orchestration layer unless the user's task genuinely requires one.
