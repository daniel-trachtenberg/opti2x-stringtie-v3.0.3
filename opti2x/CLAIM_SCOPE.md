# Claim scope — fixture-scoped QUALIFIED

## What is claimed

Under contract `stringtie-v3.0.3-serial-shortread-fixture-v2` (fixture_revision 2), the clean candidate
(`experiments/candidate-wave4-clean/`, Wave1 H5+H2+H4 + H11 + H12, **no H14**)
achieves **≥2×** wall-clock speedup vs the frozen **patched NOTHREADS baseline**
on the **named development/certification fixture**:

- Input: `benchmarks/inputs/serial_shortread_tiled.bam`
- sha256: `a1e37c767d0a4d27acf6446b3ea4727ef2fb9cc927f35cdf84990f48651e5d68`
- Envelope: `make release nothreads` (`-DNOTHREADS`); NLWP=1 observed; no `bgzf_mt`
- Timing boundary: process-start → process-exit
- Statistic: N=30 alternating paired rounds; median speedup (from medians) and
  one-sided 95% Student-t lower bound on mean(log speedup) both ≥ 2.0

**Quoted from `timing-vs-baseline/stats.json`:**

| Metric | Value |
|--------|------:|
| median speedup (medians) | **2.519009×** (~2.519×) |
| lower_95 (Student-t) | **2.490761×** (~2.491×) |
| median paired speedup | 2.513176× |
| min paired speedup | 2.342926× |
| child faster | 30/30 |

## What is **not** claimed

- **Not** a package-wide 2× claim for StringTie.
- **Not** a long-read, mixed, or multi-sample claim.
- **Not** a claim under threaded / `bgzf_mt` / multi-process envelopes.
- **Not** credit for the link-only `printCovMutex` fairness patch.
- **Not** H14 (CRC skip) — rejected; must not be in the qualified artifact.

## StringTie seed lesson

Historical seed evidence (~2.119× on a synthetic tiled short-read fixture) informed
workload design (single-process tiled BAM ≥1s). This run **re-qualified** baseline,
oracle, and resources under the frozen contract. Fixture-scoped success is **not**
permission to advertise package-level 2×.
