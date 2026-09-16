# StringTie v3.0.3 with Opti2x Optimizations

This repository is a fork of [StringTie v3.0.3](https://github.com/gpertea/stringtie) with performance optimizations applied by Opti2x.

## Summary

**Status:** QUALIFIED  
**Speedup:** ~2.52× median wall-clock time (2.519× measured, 95% CI lower bound 2.491×)  
**Scope:** Single-threaded (`NOTHREADS`) build running on `serial_shortread_tiled.bam` (tiled short-read BAM fixture, 1500 copies of `short_reads.bam`)

**This is a fixture-scoped claim, not a package-wide StringTie performance claim.** The optimizations target a specific workload (single-process, short-read, tiled BAM) and may not generalize to other use cases (long reads, multi-threaded builds, different input characteristics).

## What was optimized

Five algorithmic optimizations targeting BAM parsing and mate tracking:

- **H5**: Single-input reader fast path
- **H2**: Typed one-pass auxiliary tag parsing
- **H4**: Structured mate index
- **H11**: Reuse `GSamRecord` and `bam1_t` buffers (reduce per-read allocations)
- **H12**: Inline auxiliary tag walk (eliminate `bam_aux_next` overhead)

See [`opti2x/OPTIMIZATION_REPORT.md`](opti2x/OPTIMIZATION_REPORT.md) for full details.

## Building the optimized binary

```bash
make clean && make release nothreads
```

This produces a single-threaded `./stringtie` executable with the optimizations applied.

## Evidence and reproducibility

All evidence is in the `opti2x/` directory:

- **[`OPTIMIZATION_REPORT.md`](opti2x/OPTIMIZATION_REPORT.md)**: Speedup measurements, composition, rejected ideas, hashes
- **[`CLAIM_SCOPE.md`](opti2x/CLAIM_SCOPE.md)**: What is and is not claimed
- **[`REPRODUCE.md`](opti2x/REPRODUCE.md)**: Step-by-step instructions to rebuild and verify
- **[`optimized.patch`](opti2x/optimized.patch)**: Combined patch (fairness + all credited optimizations)
- **[`certification.json`](opti2x/certification.json)**: Machine-readable attestation with hashes and metrics

Timing was measured with N=30 alternating paired runs against a patched baseline (both built with `make release nothreads`). See [`REPRODUCE.md`](opti2x/REPRODUCE.md) for the full harness.

## Quick start for reviewers

1. **Read the claim scope first**: [`opti2x/CLAIM_SCOPE.md`](opti2x/CLAIM_SCOPE.md)
2. **Check the optimization report**: [`opti2x/OPTIMIZATION_REPORT.md`](opti2x/OPTIMIZATION_REPORT.md)
3. **Reproduce** (optional): Follow [`opti2x/REPRODUCE.md`](opti2x/REPRODUCE.md) to rebuild from the v3.0.3 pin commit and verify hashes

## What this is NOT

- **Not a 2× claim for all StringTie use cases.** This speedup applies to the specific fixture only.
- **Not for multi-threaded builds.** The optimizations target the `NOTHREADS` envelope (single-threaded execution).
- **Not for long-read or mixed workloads.** Testing was on tiled short-read data only.
- **H14 (BGZF CRC skip) was rejected** and is not included. CRC validation is retained.

## Original StringTie

For the unmodified StringTie project, see the upstream repository:  
[https://github.com/gpertea/stringtie](https://github.com/gpertea/stringtie)

**Pin commit:** `3436ad6dfd0ffc806a94086cf747ac6ff2b0dc19` (v3.0.3)
