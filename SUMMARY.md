# Green Light — Project Summary & Decision Log

This document captures the thinking behind the project so I can pick it up later without losing context. It is not for public consumption — it is a scratchpad.

---

## The original idea

Started from noticing that sandboxed execution environments (like E2B) are valuable infrastructure for AI agents. Asked: could something similar exist for economic research? The answer evolved — the interesting problem is not generic sandboxing for economists, but specifically **verifying replication packages before journal submission**.

---

## Market landscape (what already exists)

### Directly in this space
- **MetricsAI** (arxiv 2506.00856, June 2025) — an AI agent that performs econometric analysis via natural language. Open source on GitHub (FromCSUZhou/Econometrics-Agent). This is the closest thing to the original idea but is about *running new analyses*, not *verifying existing replication packages*. Different use case.
- **REPRO-Bench** — a benchmark for AI agents assessing reproducibility of social science papers. Research paper, not a usable tool.
- **Korinek NBER WP 34202** — guide for economists on building their own AI agents. Academic paper, not a product.
- **Agentic workflows for economic research** (arxiv 2504.09736) — similar territory, academic.

### Adjacent tools (not direct competition)
- Code Ocean, Posit Cloud, Binder — generic compute environments, no econ-specific compliance logic
- WRDS — data access platform, not a verification tool
- ReplicationWiki — database tracking replication studies, not automated

### The gap
No tool currently: (1) takes a replication package as input, (2) runs it automatically, (3) checks it against a specific journal's requirements, and (4) tells you what to fix before you submit. This workflow is entirely manual today. The AEA only appointed its first Data Editor in July 2023 — the whole infrastructure is new and primitive.

---

## The Stata problem and how we resolved it

Stata is the dominant language in economics replication packages. It is proprietary — you cannot run it in a container without a valid license.

**Options considered:**
1. BYOL (Bring Your Own License) — user uploads their `stata.lic` alongside the package. Legally clean. Creates some friction but researchers submitting to journals almost certainly have a license.
2. R and Python only for MVP — ship faster, add Stata in v2.
3. University partnership — use Groningen's site license for research purposes. Slow bureaucratically.

**Decision: Option B for MVP (R and Python only).** Design the container routing architecture so BYOL Stata is easy to add in v2 without restructuring.

**Key finding:** The AEA Data Editor has already published Docker images for Stata (AEADataEditor/docker-stata). The technical problem is solved — it is purely a licensing and scoping decision.

---

## Build plan

### Stage 1 — Sandboxed execution (weeks 1–3)
- Accept a zip upload
- Extract into a Docker container (Python or R image depending on detected language)
- Run the main script, capture stdout, stderr, exit code
- Return raw execution log
- This alone is useful — most packages fail here

### Stage 2 — Journal compliance checks (weeks 3–5)
- Hard-code AEA requirements first (biggest journal, most standardized)
- Rule-based parser: does a README exist, does it mention data sources, are data citations present, are file formats compliant
- Output structured JSON report with pass/fail per check
- No AI yet — just a linter

### Stage 3 — AI fix suggestions (weeks 5–8)
- Feed Stage 1 error output and Stage 2 failed checks to Claude API
- Generate specific, actionable suggestions in plain English
- Example: "Your R script calls `fixest` but it is not in your renv.lock — add it with `renv::snapshot()` after installing"
- This is what makes it genuinely useful vs just a linter

### Stage 4 — Open source release (week 8+)
- Clean README, demo video
- Post on EconTwitter, AEA forums
- Tag journal data editors — they are the exact audience who would share this
- Lars Vilhuber (AEA Data Editor) is the key person in this community

---

## Checks to do before writing code

1. **Talk to one real economist** — find a PhD student or postdoc who has submitted a replication package in the last two years. Ask: where did the process actually hurt, what did the data editor come back with. Dr. Gelsomino or Dr. Vullings at Groningen can introduce me to someone. This might shift assumptions.

2. **Run real packages manually** — download 2–3 packages from Zenodo (AEA or Econometric Society community) and try to run them on my own machine. This reveals actual failure modes: missing dependencies, hardcoded paths, proprietary data gaps. Tells me what the sandbox really needs to handle.

3. **Study AEA requirements in detail** — read the full AEA data and code availability policy before writing the compliance checker. The rule-based checks in Stage 2 need to be grounded in the actual policy document, not my guess of what it says.

---

## Why this is a good fit for me specifically

- I understand the domain — I know what IV-2SLS, DiD, RDD are, I have done empirical research, I know what a replication package is supposed to contain
- I am in the ecosystem — Honours programme, research projects, direct access to researchers who submit to journals
- It is genuinely buildable — the core MVP is a Python script + Docker + a rule-based checker. No exotic infrastructure required.
- The learning curve is the point — I need to learn Python agent pipelines anyway. This gives me a concrete project to learn through.

---

## Things I do not know yet

- What is the actual most common failure mode in replication packages? (Need to talk to economists and run packages manually)
- How strict is Stata's licensing when it comes to educational/research use of Docker? (Need to read the license terms carefully if we ever add BYOL)
- Is there appetite in the econ community for a tool like this, or do researchers see the data editor process as someone else's problem? (Need to validate)
- What does the AEA's internal tooling look like? Is Lars Vilhuber already building something similar internally?

---

## Next immediate actions

1. Download and attempt to run 2–3 real replication packages from Zenodo
2. Read the full AEA data and code availability policy
3. Find one economist to talk to about the submission pain
4. Set up Python environment and write the first Docker container wrapper (Stage 1 start)
