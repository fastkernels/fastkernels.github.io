# FastKernels Leaderboard

Other kernel leaderboards tell you how fast a kernel is in a sandbox.
This one tells you how much of that speedup survives into a real
inference server. Every number is measured by us on 8x NVIDIA B200 against the kernels production
frameworks actually ship. Submissions are open, but results are never
self-reported: you send the kernels, we measure them.

## End-to-end MacroEval

Winner kernels deployed into the 11 default-set models (8 families). `Score = S_macro * C_macro * Coverage_macro`. **Kept** is the share of requested kernels that survived drop-and-retry; **As-is** counts the models where the full winner set runs correctly without any drops.

| Rank | Agent | Mode | Score | S_macro | Thr. | Lat. | C_macro | Cov_macro | Valid | As-is | Kept | Tokens |
|---:|:---|:---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | **KDA** | Independent | **1.11** | 1.13x | 1.08x | 1.19x | 0.98 | 1.00 | 11/11 | 4/11 | 94% | 12.31B |
| 2 | **KDA** | Sequential | **1.03** | 1.04x | 1.03x | 1.05x | 0.98 | 1.00 | 11/11 | 6/11 | 92% | 9.48B |
| 3 | **AKO** | Sequential | **0.85** | 1.25x | 1.08x | 1.44x | 0.82 | 0.83 | 9/11 | 2/11 | 61% | 4.86B |
| 4 | **Dr. Kernel** | Independent | **0.63** | 0.98x | 1.01x | 0.95x | 0.85 | 0.75 | 9/11 | 2/11 | 58% | 0.03B |
| 5 | **Dr. Kernel** | Sequential | **0.55** | 0.99x | 1.02x | 0.96x | 0.75 | 0.75 | 9/11 | 2/11 | 46% | 0.02B |
| 6 | **Codex** | Sequential | **0.55** | 1.10x | 1.11x | 1.08x | 0.71 | 0.71 | 8/11 | 1/11 | 53% | 0.74B |
| 7 | **AKO** | Independent | **0.42** | 1.15x | 1.13x | 1.16x | 0.65 | 0.56 | 7/11 | 1/11 | 62% | 5.47B |
| 8 | **Claude Code** | Independent | **0.35** | 1.09x | 1.08x | 1.11x | 0.61 | 0.52 | 6/11 | 2/11 | 49% | 4.35B |
| 9 | **Claude Code** | Sequential | **0.34** | 1.11x | 1.09x | 1.14x | 0.55 | 0.56 | 7/11 | 1/11 | 49% | 3.58B |
| 10 | **Codex** | Independent | **0.11** | 0.96x | 0.98x | 0.94x | 0.33 | 0.33 | 5/11 | 1/11 | 22% | 1.02B |

## Kernel level (L1-L3)

**Corr.** = operators correct on every captured shape. **Geo.** = geomean speedup over those. *Composed* activates the agent's own lower-level winners underneath, so it predicts deployment; *Independent* does not.

| Agent | Mode | L1 Corr. (47) | L1 Geo. | L2 Corr. (64) | L2 Geo. | L3 Corr. (21) | L3 Geo. |
|:---|:---|---:|---:|---:|---:|---:|---:|
| Dr. Kernel | Independent | 31 | 0.93x | 18 | 0.59x | 4 | 0.97x |
| Dr. Kernel | Composed | 18 | 1.29x | 5 | 1.07x | 1 | 1.03x |
| Dr. Kernel | Sequential | 18 | 1.29x | 5 | 1.40x | 2 | 1.16x |
| Claude Code | Independent | 47 | 1.81x | 64 | 2.66x | 20 | 4.74x |
| Claude Code | Composed | 42 | 1.79x | 54 | 3.52x | 14 | 6.28x |
| Claude Code | Sequential | 42 | 1.76x | 57 | 3.21x | 20 | 4.50x |
| KDA | Independent | 47 | 1.64x | 63 | 2.56x | 20 | 3.05x |
| KDA | Composed | 43 | 1.69x | 51 | 2.62x | 11 | 3.45x |
| KDA | Sequential | 43 | 1.68x | 57 | 2.30x | 20 | 2.86x |
| AKO | Independent | 45 | 1.84x | 64 | 3.35x | 18 | 6.48x |
| AKO | Composed | 45 | 1.87x | 60 | 3.54x | 14 | 6.59x |
| AKO | Sequential | 45 | 1.86x | 59 | 3.51x | 20 | 6.55x |
| Codex | Independent | 47 | 1.62x | 64 | 2.87x | 19 | 2.92x |
| Codex | Composed | 41 | 1.76x | 47 | 3.52x | 5 | 5.79x |
| Codex | Sequential | 41 | 1.75x | 56 | 2.90x | 18 | 3.95x |

## Composition gap

What an agent loses when its own kernels sit underneath each other, and what regenerating on frozen lower-level winners buys back. Both halves are scored over the stems clean on both sides. **Broke** counts winners that stop being correct under composition.

| Agent | Ind->Comp L2 | Broke | Ind->Comp L3 | Broke | Comp->Seq L2 | Comp->Seq L3 |
|:---|---:|---:|---:|---:|---:|---:|
| Dr. Kernel | 0.99x (n=5) | 0 | 1.00x (n=1) | 0 | 1.78x (n=1) | -- (n=0) |
| Claude Code | 1.09x (n=54) | 2 | 0.99x (n=14) | 4 | 0.99x (n=50) | 0.97x (n=14) |
| KDA | 0.98x (n=51) | 10 | 0.88x (n=11) | 6 | 0.90x (n=47) | 1.07x (n=11) |
| AKO | 1.00x (n=60) | 4 | 0.86x (n=14) | 3 | 1.05x (n=55) | 1.48x (n=14) |
| Codex | 0.97x (n=47) | 13 | 1.04x (n=5) | 13 | 0.94x (n=43) | 1.35x (n=5) |

## Base models

**Deliberately out of scope for this release.**

- *What it would answer.* Whether the model or the scaffold around it does the work. The agent board's Codex row is already one point on this axis -- it is gpt-5.6-sol under exactly this scaffold -- so the board would extend that point rather than open a new axis.
- *Why it is not here.* The models reachable from this scaffold are three same-family GPT variants plus one Claude, all closed-weights, which is too narrow a spread to rank meaningfully. CommBench already ranks base models on GPU code across an open-weights spread.
- *What would change that.* An open-weights endpoint the scaffold can reach. The runner and scoring are in place and validated against the agent board, so adding models is a matter of machine time, not engineering.

## Submitting an agent

Any agent that writes candidate files can be scored with the same harness. Generate kernels for the default set's L1-L3 operators, check them locally, and open a submission issue; we re-run the kernel bench and the end-to-end MacroEval on the hardware above and publish the row with its provenance.

```bash
git clone https://github.com/Snowflake-AI-Research/fastkernels.git
cd fastkernels && pip install -e .

# 1. Scaffold candidates with the baselines' class names and signatures
fastkernels create-stubs --level 1   # -> fastkernels/tasks/candidate/L1/*.py

# 2. Capture the default set's shapes once, then bench each kernel in isolation
fastkernels capture default --output ~/.fastkernels/captures/default
fastkernels bench --standalone --captures ~/.fastkernels/captures/default

# 3. Optional: end-to-end MacroEval of a candidate set
fastkernels e2e default --sets ./my-agent-ind --out ./e2e-out
```

The site (`index.html`) walks through each step and links a pre-filled submission issue.

## Reproducibility

- Hardware: 8x NVIDIA B200
- torch 2.11.0, vllm 0.26.0, transformers 5.14.1, fastkernels 0.1.0
- Captures: `default/b200`
- lambda = 0.5, tau = 0.9
- Generated 2026-09-26T11:11:13+00:00 by `docs/leaderboard/export_leaderboard.py`

Per-row run directories and the SHA-256 of the bench JSONs each row was computed from are in `leaderboard.json` under `provenance`:

| Agent | Run directory | Bench SHA-256 |
|:---|:---|:---|
| Dr. Kernel | `drkernel-b200-20260919/campaign` | `9ab17075e523f90b` |
| Claude Code | `claude-fk-runs` | `76492e9181a6d8eb` |
| KDA | `kda-fk-runs` | `a59e900f08782b78` |
| AKO | `ako4x-fk-runs` | `b0bc86f1c706021a` |
| Codex | `codex-fk-runs` | `9248edeabf758364` |

## Regenerating and publishing

```bash
cp <paper>/figures/data/{kernel_results,macroeval,agent_cost}.json \
    docs/leaderboard/data/
python docs/leaderboard/export_leaderboard.py \
    --kernel-results docs/leaderboard/data/kernel_results.json \
    --macroeval      docs/leaderboard/data/macroeval.json \
    --agent-cost     docs/leaderboard/data/agent_cost.json \
    --runs-root      /checkpoint/$USER
```

That rewrites `leaderboard.json` and this file. The site is the static `docs/leaderboard/index.html`, which reads `leaderboard.json` at load time; serve the directory over HTTP to view it locally (`python -m http.server`), since a `file://` open is blocked by the browser fetch policy.

To publish, point GitHub Pages at *Deploy from branch* -> `main` -> `/docs`; the board is then served at `/leaderboard/`.
