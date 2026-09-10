# Opti2x Stage 9–10 report — StringTie v3.0.3

## Terminal status: **QUALIFIED**

**Contract:** `stringtie-v3.0.3-serial-shortread-fixture-v2`  
**Fixture revision:** 2  
**Candidate:** `experiments/candidate-wave4-clean/`  
**When packaged (PT):** 2026-09-09 2:33 PM PT  
**When packaged (UTC):** 2026-09-09T21:33:04Z

Fixture-scoped claim only. See `CLAIM_SCOPE.md`.  
**H14 is rejected and absent** from this deliverable.

---

## Speedup + CI (N=30 paired vs frozen baseline)

Evidence: `experiments/candidate-wave4-clean/timing-vs-baseline/{stats,summary}.json`  
Harness: `experiments/_helpers/timed_pair_v2.sh` (warmup 1 + 30 alternating paired rounds).

| Metric | Value (from stats.json) |
|--------|------------------------:|
| Parent (baseline) median wall | 1.776823 s |
| Child (clean) median wall | 0.705366 s |
| **Median speedup (medians)** | **2.519009× ≈ 2.519×** |
| Median paired speedup | 2.513176× |
| Min paired speedup | 2.342926× |
| Child faster | **30/30** |
| **One-sided 95% Student-t lower bound** | **2.490761× ≈ 2.491×** |
| Estimator | one-sided Student-t 95% lower bound on mean(log(parent/child)) |
| t_crit (df=29, α=0.05 one-sided) | 1.699127026533495 |

Both median ≥ 2.0 and lower_95 ≥ 2.0 under the frozen acceptance gates.

---

## Claim scope

**QUALIFIED means ≥2× on `serial_shortread_tiled.bam` under the NOTHREADS envelope** —
not a package-wide StringTie claim. Seed lesson: synthetic tiled short-read success
does not generalize to long-read / multi-thread / whole-package marketing.

---

## Composition (credited optimizations)

| Layer | Hypotheses | Role |
|-------|------------|------|
| Wave1 | **H5** + **H2** + **H4** | single-input reader fastpath; typed one-pass aux tags; structured mate index |
| Wave4 | **H11** | reuse `GSamRecord` + `bam1_t` (reduce per-read alloc) |
| Wave4 | **H12** | inline aux walk (cut `bam_aux_next` overhead) |

**Retained but not credited:** link-only `printCovMutex` `#ifndef NOTHREADS` guards
(`fairness-baseline.patch`) — fairness so NOTHREADS baseline links.

**Build:** `make clean && make release nothreads`  
**NOTHREADS:** yes. **NLWP observed:** 1. **No `bgzf_mt`.** CRC validation retained.

---

## Rejected ideas (esp. H14)

| ID | Decision | Why |
|----|----------|-----|
| **H14_bgzf_skip_crc** | **rejected** | **`contract_forbidden: skips BGZF CRC validation`**. Oracle can pass on valid BAM while skipping I/O integrity. Corrupt-trailer contrast: baseline + clean detect CRC mismatch; H14 silent-accepts. **Not in this package.** |
| H1_count_good_junctions | rejected | ~1.006× noise |
| H3 / H3b pools | rejected | <1.02× / regression vs wave1 |
| H7 bitset temps | rejected | insufficient speedup |
| H8 UF path compression | rejected | insufficient |
| H2b / H4b / H10 | rejected | regressions vs wave1 on v2 |
| H6b libdeflate reuse | rejected | ~0.992×; Huffman tables per-block |
| H13 mate name arena | rejected | segfault (arena move under live MateKey pointers) |

Full ledger: `deliverable/hypotheses.jsonl` (copy of run ledger).  
Adversarial: `experiments/ADVERSARIAL_REVIEW.md`.

---

## Limitations (explicit)

1. **cgroup `pids.max` unenforceable on this host** — `/sys/fs/cgroup/.../pids.max` not writable from agent. Full envelope `cgroup-v2-pids-max-1-plus-creation-events` not enforced here.
2. **NLWP=1 observed** at process level (spot-check), not cgroup-attested max tasks.
3. **Agent-attested packaging** on this host — not an independent external held-out evaluator appliance. Evidence hashes + adversarial review recorded; evaluator_note in `certification.json`.
4. Fixture-scoped only (`serial_shortread_tiled.bam`); deprecated process-spawn tile is not the claim.
5. Prior `candidate-wave4` (with H14) is **superseded** and must not be shipped as qualified.

---

## Hashes

| Artifact | sha256 |
|----------|--------|
| Candidate binary | `d545ffe246c5231447b20acb2a83192abed536c42d77dccfa61f9f684b236cc7` |
| Baseline binary (patched NOTHREADS) | `d2f4af1668e58b6f7f1f431a87343c1bff8a728b449896d781c5429621e9c2b0` |
| `optimized.patch` (pin + fairness + algos) | `1d74b48a46e33e980c1bc229f3edeb3733b6da6c92c58d7ab4e8d62fd3ab3fb3` |
| `fairness-baseline.patch` | `a51937fe305cc9b5ff16b7b6323edc68b58ac0c59ce806f2ade8463070ceee06` |
| `optimized-vs-patched-baseline.patch` | `e46bc48a779df4d3e94a7810eebc54088e8da711c37f376ab5ce3818fb08fb6e` |
| Input `serial_shortread_tiled.bam` | `a1e37c767d0a4d27acf6446b3ea4727ef2fb9cc927f35cdf84990f48651e5d68` |
| Oracle strip-^# | `f0a9544954c38a273012837e7689ed2c6fd1c074b21edc86858505c6a20af9ef` |
| Pin commit | `3436ad6dfd0ffc806a94086cf747ac6ff2b0dc19` |
| Contract `optimization.yaml` | `8a550b3d0f3b54c8c75ef80626cf61bc3ed5ac6b3ffb07e2b77785ae34fe785d` |

---

## Commands (summary)

```bash
# Build candidate (from patched or from pin+optimized.patch)
make clean && make release nothreads

# Run argv (primary fixture)
./stringtie -o out.gtf /path/to/serial_shortread_tiled.bam

# Oracle compare (strip comment headers)
grep -v '^#' out.gtf | sha256sum
# expect: f0a9544954c38a273012837e7689ed2c6fd1c074b21edc86858505c6a20af9ef

# Timing: see REPRODUCE.md / experiments/_helpers/timed_pair_v2.sh
```

Profiles: `profiles/PROFILE_tiled_bam.md`, `profiles/PROFILE_candidate_v2.md`.  
Compare table: `experiments/candidate-wave4-clean/COMPARE.md`.
