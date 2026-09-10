# Reproduce — StringTie v3.0.3 Opti2x QUALIFIED candidate

Contract: `stringtie-v3.0.3-serial-shortread-fixture-v2`  
Pin: `3436ad6dfd0ffc806a94086cf747ac6ff2b0dc19` (`v3.0.3`)  
Candidate binary sha256: `d545ffe246c5231447b20acb2a83192abed536c42d77dccfa61f9f684b236cc7`

## 1. Clone and checkout pin

```bash
git clone https://github.com/gpertea/stringtie.git
cd stringtie
git checkout 3436ad6dfd0ffc806a94086cf747ac6ff2b0dc19
# optional verify: git rev-parse HEAD  →  3436ad6dfd0ffc806a94086cf747ac6ff2b0dc19
```

## 2. Apply patches

### Preferred: single combined patch (fairness + optimizations)

```bash
patch -p1 < /path/to/opti2x/deliverable/optimized.patch
# sha256: 1d74b48a46e33e980c1bc229f3edeb3733b6da6c92c58d7ab4e8d62fd3ab3fb3
```

### Or split:

```bash
patch -p1 < /path/to/opti2x/deliverable/fairness-baseline.patch   # link-only; NOT credited
# sha256: a51937fe305cc9b5ff16b7b6323edc68b58ac0c59ce806f2ade8463070ceee06
patch -p1 < /path/to/opti2x/deliverable/optimized-vs-patched-baseline.patch
# sha256: e46bc48a779df4d3e94a7810eebc54088e8da711c37f376ab5ce3818fb08fb6e
```

**Do not apply H14.** No BGZF CRC skip patch is part of this deliverable.

## 3. Build

```bash
make clean && make release nothreads
# Produces ./stringtie
# Do NOT pass CXXFLAGS= on the Make CLI (strips BASEFLAGS).
sha256sum stringtie
# expected candidate: d545ffe246c5231447b20acb2a83192abed536c42d77dccfa61f9f684b236cc7
# (exact match requires same toolchain/deps as this host; semantics should match)
```

Frozen baseline on this run (patched pin, no algos): `d2f4af1668e58b6f7f1f431a87343c1bff8a728b449896d781c5429621e9c2b0`.

## 4. Input

Use the frozen tiled BAM (or reconstruct per `benchmarks/FIXTURE_TILED_BAM.md`):

- Path (this run): `benchmarks/inputs/serial_shortread_tiled.bam`
- sha256: `a1e37c767d0a4d27acf6446b3ea4727ef2fb9cc927f35cdf84990f48651e5d68`
- Construction: `samtools cat` of N=1500 copies of `short_reads.bam` (stringtie-testdata v3.0.2), then `samtools sort`

## 5. Run argv

```bash
./stringtie -o serial_shortread_tiled.out.gtf serial_shortread_tiled.bam
```

## 6. Oracle compare (strip `^#`)

```bash
grep -v '^#' serial_shortread_tiled.out.gtf | sha256sum
# expect: f0a9544954c38a273012837e7689ed2c6fd1c074b21edc86858505c6a20af9ef
```

Comparator: exact-files after removing lines matching `^#` only.  
Deterministic_runs gate used in-run: ×3 PASS.

## 7. Timing recipe (paired vs baseline)

Build **both** binaries the same way (`make release nothreads`):

- Parent = pin + fairness-only (`fairness-baseline.patch`)
- Child  = pin + `optimized.patch` (or fairness + algo-only)

```bash
# From run root (paths relative to this Opti2x run tree):
bash experiments/_helpers/timed_pair_v2.sh \
  baseline/stringtie \
  experiments/candidate-wave4-clean/worktree/stringtie \
  /tmp/timing-out \
  30
```

- Warmup: 1 parent + 1 child
- Rounds: 30 alternating order (odd: P then C; even: C then P)
- Wall = process-start → process-exit (`time.perf_counter` around `subprocess.run`)
- Page cache: warm (both sides run after warmup)
- Aggregate with the same estimator as `timing-vs-baseline/stats.json`
  (one-sided Student-t 95% lower bound on mean(log(parent/child)))

Expected ballpark on this host class: median ~2.519×, lower_95 ~2.491×  
(exact walls vary by machine; re-run N=30 on your host before citing).

## 8. Resource spot-check

```bash
# During a run, observe NLWP (expect 1 for NOTHREADS binary)
# cgroup pids.max may be unenforceable — document if so (this host: unwritable)
```

Corrupt-BAM CRC check should still fail like upstream (H14 absent).
