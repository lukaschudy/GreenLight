# Green Light

> An open-source tool that automatically verifies economics replication packages before journal submission.

## The problem

Submitting a replication package to an economics journal is painful. Researchers manually zip their code and data, fill in checklists, and submit — only to have a data editor come back weeks later with a list of things that don't run or don't comply with the journal's requirements. The process is slow, manual, and entirely avoidable.

Green Light solves this by running your package automatically in a sandboxed environment and producing a compliance report before you submit.

## What it does

1. You upload a zip file containing your code, data, and README
2. Green Light runs your code in an isolated Docker container
3. It checks execution (does the code actually run?), outputs (do results match what the paper claims?), and journal compliance (README format, data citations, file structure)
4. It returns a structured report with pass/fail per check and specific fix suggestions

## Scope (v1)

**Supported languages:** R and Python only.

Stata support is technically feasible via Docker (the AEA Data Editor has already built the required images) but requires a valid Stata license. v1 uses a BYOL (Bring Your Own License) architecture internally so Stata can be added in v2 without restructuring. For now, R and Python cover a growing share of modern replication packages.

**Supported journals (planned):** AER first, then QJE and Econometrica. Each has slightly different README and data citation requirements.

## Build stages

| Stage | What | When |
|-------|------|-------|
| 1 | Sandboxed execution — run code in Docker, capture stdout/stderr/exit code | Weeks 1–3 |
| 2 | Journal checklists — rule-based compliance checks for AER requirements | Weeks 3–5 |
| 3 | AI fix suggestions — Claude API interprets errors and suggests specific fixes | Weeks 5–8 |
| 4 | Open source release — GitHub, demo, outreach to econ community | Week 8+ |

## Why this and not something else

- **MetricsAI / econometrics agents** — these help you *run* analysis. Green Light checks that analysis you've *already done* actually reproduces.
- **Code Ocean / Posit Cloud** — generic compute environments. No journal-specific compliance logic, no AI fix suggestions, no econ-domain awareness.
- **Manual submission** — the status quo. Slow, error-prone, and the data editor bottleneck is getting worse as journals tighten reproducibility requirements.

## Prior art acknowledged

- [AEADataEditor/docker-stata](https://github.com/AEADataEditor/docker-stata) — Docker setup for Stata, built by the AEA Data Editor. We build on this architecture.
- [REPRO-Bench](https://arxiv.org/abs/...) — benchmark for AI reproducibility assessment in social science. Validates the problem space.
- MetricsAI (arxiv: 2506.00856) — econometrics AI agent. Different use case (running new analysis vs verifying existing packages).

## Tech stack (planned)

- Python (orchestration, API layer)
- Docker (sandboxed execution)
- Claude API (fix suggestions in Stage 3)
- R and Python containers (execution environments)

## Status

Pre-MVP. Currently in planning and research phase.

## Contributing

Not open for contributions yet — waiting until there is something to contribute to. Star/watch the repo to follow progress.

## Author

Lukas Chudy — BSc Economics (minor Mathematics), University of Groningen. Incoming MSc Mathematics and Computation, LSE.
