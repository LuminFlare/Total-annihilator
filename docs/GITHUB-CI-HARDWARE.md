# GitHub Actions hardware, concurrency and artifact policy

Effective 2026-10-17. Scope: LuminFlare/Total-annihilator (Static HTML). Every future GitHub/Codex task changing CI, performance, caching, or evidence must consult this file before implementation.

## Shared physical machine and actual access

- Known host: AMD Ryzen 7 7800X3D, dedicated HavilandVM Ubuntu runner `haviland-vf-ci-01`, 8 virtual CPUs, Hyper-V dynamic memory (maximum 12 GiB), approximately 110 GiB disk free at deployment. **Re-measure** actual CPU/RAM/disk each run; 12 GiB maximum is not guaranteed immediately available RAM.
- This runner is currently positively verified for **LuminFlare/voidflare-status**, with `[self-hosted,linux,x64,vf-ci]`. Its accessibility from **this** repository is not yet established; do NOT switch `runs-on` to the self-hosted label until explicit repository authorization/runner availability and fork security controls are verified.
- Single runner process = one GitHub job at a time. Repository `concurrency` is not global cross-repository resource arbitration; do not add independent parallel processes sharing this VM without an authorized global scheduler. Do not grant production secrets/mounts to the persistent runner; enforce trusted same-repo branches and disable untrusted fork workflows at GitHub settings.

## Default execution budget

- Dedicated **self-hosted** regular parallelizable CPU/test jobs: **4 workers**, allow **6 workers** only if at least eight effective CPUs and **10 GiB genuinely available RAM**, no swap pressure, no concurrent heavy job; reserve OS headroom.
- Short or single-threaded workflows and **GitHub-hosted** runners retain the correct existing per-platform concurrency. Do not set 6 workers on a two-vCPU hosted runner.
- Preserve source SHA/lockfiles/pinned tools, all tests, negative controls, timeouts, reliability, and security/approval conditions. First prove the 4-worker candidate; compare 4 versus 6 under the exact same SHA, test inventory, environment and workload. Any unexpected race, false negative, OOM, artifact loss or performance regression blocks rollout.
- Never replace Windows-specific tests with Linux checks, alter a ZK proof-memory contract or relax formal scientific holdout gates simply to reach the numeric worker target.

## Project-specific binding

- This repository currently contains static HTML files and no checked-in GitHub Actions workflows. Do not add compute-heavy CI or a self-hosted runner for no benefit. If CI is added later, use lightweight hosted validation unless a trusted, authorized runner is available.
- Browser/E2E tests added in the future can evaluate 4 Workers on a qualifying self-hosted machine and up to 6 with free RAM >=10 GiB; first verify independent test cases and deterministic output.

## Artifacts and sustainable storage

- Routine reproducible, synthetic transient browser/diagnostic outputs: target **3 days** retention in new workflows. Scientific, security, SP1, provenance, formal approval or unique evidence must retain its separately specified lifetime, and any authoritative evidence should be committed (when appropriate) to a suitable repository evidence path or protected long-term storage.
- Before deleting existing GitHub artifacts inventory **name, ID, source workflow, run, timestamps, size and proof role**; delete only confirmed superseded disposable artifacts. Never delete original source, secrets, unique gate/proof/holdout evidence or active-PR evidence.
- Artifacts upload failure != evidence delivery. Cleanup can take hours to be reflected in GitHub storage accounting; caches and plan limits also matter.
- Future CI should record CPU/RAM/swap/disk, elapsed time, actual runner, source revision, full correctness result and upload status. Propose changes as reviewable PRs; do not automatically merge, deploy, charge paid CI, or relax existing permissions.

## Future task intake checklist

Read this file and existing project `AGENTS.md`/status first → verify runner access and available resources → preserve frozen/protected gates → choose bounded per-process workers → run the same tests with actual runtime evidence → report elapsed time, pass/fail and artifact health → update the budget only after validated evidence.
