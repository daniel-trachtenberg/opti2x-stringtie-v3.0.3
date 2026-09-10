# Opti2x optimized StringTie v3.0.3

This repository contains an Opti2x-optimized StringTie **v3.0.3** source tree.

**Claim (qualified):** ~**2.52×** median speedup on a **serial short-read tiled BAM** fixture. This is **NOT** a package-wide claim.

## Build

```bash
make clean && make release nothreads
```

## Documentation

See `opti2x/OPTIMIZATION_REPORT.md` for methodology, claim scope, reproduction steps, and evidence links. Additional Opti2x artifacts live under `opti2x/`.
