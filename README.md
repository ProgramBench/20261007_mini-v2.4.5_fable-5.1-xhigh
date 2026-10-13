# Claude Fable 5.1 (xhigh) — ProgramBench submission

- **Model:** `claude-fable-5-1`, reasoning effort **xhigh**, max 65,536 output tokens per call
- **Scaffold:** mini-SWE-agent v2.4.5 (single-agent), open source; step limit 1,000, no cost
  limit, 48 h wall-time limit
- **Protocol:** no-internet + `task_cleanroom_v6` images. The agent sees only the compiled binary
  (source removed, `.git` reset), reimplements it from scratch by interacting with it, and never
  has network access (`--network none` at the container level).
- **Score:** ~0.825 (mean per-instance test pass-rate over the fixed 200, missing = 0; the
  leaderboard recomputes authoritatively from `_stats/score.json`). Only tests listed in the
  benchmark's `tests.json` are scored.
- **Inference dates:** 2026-10-07 to 2026-10-09

## Task accounting

196 tasks are evaluated and included here. Four are scored 0 and have no directory:
`facebook__zstd` and `madler__pigz` never produced a submission, and `esubaalew__run` and
`unhappychoice__gittype` ended on repeated provider errors with no usable work.

Only provider/infrastructure failures were retried manually. Rollouts where the model stopped
without making a tool call (`RepeatedFormatError`, 12 tasks) or the provider rejected a request
(`BadRequestError`, 21 tasks) received only mini-SWE-agent's built-in retries. However a rollout
ended, its final workspace was evaluated. Each task ships the trajectory and solution of the
attempt that was scored.

`filosottile__age`: the standard evaluator fails on this task when a submission leaves Go or C
sources in the workspace, because the hidden test branches build reference tools in place. It
was re-evaluated with a fix that removes the submission's Go/C sources after compilation and
before the test branches run; the compiled executable is unchanged.

## Extra stats

Cost, model-call, and output-token statistics are generated from the published trajectories by
the scripts under `_scripts/`. Cost is recomputed from each response's reported token usage at
Anthropic's list rates for Claude Fable 5.1: $10 per million uncached input tokens, $0.25 per
million cached input tokens, and $50 per million output tokens.

## Redactions

Harness metadata naming internal hosts and users is replaced with `[REDACTED]` in each
trajectory's `info.config` (`safety_identifier`, `litellm_model_registry`), and host paths in
harness tracebacks are rewritten to `/harness/`. Model and tool turns are unmodified.

Each test is run up to 3× (pytest-rerunfailures); only the final attempt is scored (flakiness defense).
