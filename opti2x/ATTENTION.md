# Attestation digest — QUALIFIED (fixture-scoped)

**Do not weaken.** H14 is not part of this package.

## Immutable-ish digests

| Object | sha256 / id |
|--------|-------------|
| `certification.json` (canonical key-sorted) | `b4b342c21137a5d10733671689d909393b2308f3ccc03484de8780fb83b8fa95` |
| Candidate binary | `d545ffe246c5231447b20acb2a83192abed536c42d77dccfa61f9f684b236cc7` |
| `optimized.patch` | `1d74b48a46e33e980c1bc229f3edeb3733b6da6c92c58d7ab4e8d62fd3ab3fb3` |
| `fairness-baseline.patch` | `a51937fe305cc9b5ff16b7b6323edc68b58ac0c59ce806f2ade8463070ceee06` |
| `optimized-vs-patched-baseline.patch` | `e46bc48a779df4d3e94a7810eebc54088e8da711c37f376ab5ce3818fb08fb6e` |
| Input BAM | `a1e37c767d0a4d27acf6446b3ea4727ef2fb9cc927f35cdf84990f48651e5d68` |
| Oracle strip-^# | `f0a9544954c38a273012837e7689ed2c6fd1c074b21edc86858505c6a20af9ef` |
| Pin | `3436ad6dfd0ffc806a94086cf747ac6ff2b0dc19` |
| Contract yaml | `8a550b3d0f3b54c8c75ef80626cf61bc3ed5ac6b3ffb07e2b77785ae34fe785d` |
| Attestation document | `ae8f1a6e9800a2a92b760d825fdb52a71fa5035908c01be93896af5624f9bcd5` |

## Quoted gates

- median speedup (medians): **2.519009×** (~2.519×)
- lower_95: **2.490761×** (~2.491×)
- N=30; child faster 30/30

## Limitations

- cgroup `pids.max` unenforceable on this host
- NLWP=1 observed (process), not cgroup-enforced
- Agent-attested on this host
- Fixture-scoped ≠ package-wide 2×

Machine-readable: `attestation.json`, `certification.json`.
