# Patch layout — candidate-wave4-clean

**Pin:** `3436ad6dfd0ffc806a94086cf747ac6ff2b0dc19` (`v3.0.3`)  
**Candidate:** `experiments/candidate-wave4-clean/`  
**H14:** **not included** (`contract_forbidden: skips BGZF CRC validation`)

## Preferred single patch (vs pristine public pin)

| File | sha256 | Applies to |
|------|--------|------------|
| `optimized.patch` | `1d74b48a46e33e980c1bc229f3edeb3733b6da6c92c58d7ab4e8d62fd3ab3fb3` | pristine `source/stringtie` at pin |

Contains:
1. **FAIRNESS / link-only (not credited):** `#ifndef NOTHREADS` guards around `printCovMutex` extern + lock/unlock in `rlink.cpp` (same intent as `fairness-baseline.patch`).
2. **OPTIMIZATIONS (credited):** Wave1 = H5 + H2 + H4; plus H11; plus H12.

Apply from checkout root:
```bash
git checkout 3436ad6dfd0ffc806a94086cf747ac6ff2b0dc19
patch -p1 < /path/to/deliverable/optimized.patch
# or: git apply --whitespace=nowarn optimized.patch
make clean && make release nothreads
```

## Split form (optional)

| File | sha256 | Role |
|------|--------|------|
| `fairness-baseline.patch` | `a51937fe305cc9b5ff16b7b6323edc68b58ac0c59ce806f2ade8463070ceee06` | link-only vs pin |
| `optimized-vs-patched-baseline.patch` | `e46bc48a779df4d3e94a7810eebc54088e8da711c37f376ab5ce3818fb08fb6e` | algos only vs patched baseline |

Order: fairness first, then algo-only.

## Files touched (optimization + fairness)

`bundle.h`, `gclib/GSam.cpp`, `gclib/GSam.h`, `rlink.cpp`, `stringtie.cpp`, `tmerge.cpp`, `tmerge.h`  
No `htslib/` / BGZF CRC changes.
