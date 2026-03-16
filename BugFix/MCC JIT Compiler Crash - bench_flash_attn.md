# Flash Attention Benchmark - JIT Compiler Issue Tracker

**Tracking:** MCC JIT compiler crashes and benchmark results across MTCC commits  
**Server:** 10.18.32.15 (mccxadmin)  
**Container:** mtcc-mate-perf  
**Path:** /home/devuser/yangfan/perf/mate

**Test Command:**
```bash
MATE_FORCE_JIT=1 TVM_FFI_MUSA_ARCH_LIST=3.1 python benchmarks/bench_flash_attn.py
```

**Note:** Always verify commit with `mcc --version` before each benchmark run!

---

## Benchmark Results Summary

| Date | Time | MTCC Commit | Run # | Status | IsCausal | IsPackGQA | BatchSize | TotalSeqlenQ | HeadQ | HeadKV | Bandwidth (GB/s) | Compute (TFLOPS) | Notes |
|------|------|-------------|-------|--------|----------|-----------|-----------|--------------|-------|--------|------------------|------------------|-------|
| 2026-03-16 | 16:51 | 3c5851dee | #1 | ❌ Crash | - | - | - | - | - | - | - | - | LLVM assert failure |
| 2026-03-16 | 17:27 | 41c0ba6 | #2 | ✅ Pass | True | True | 56 | 229376 | 64 | 4 | 138.86 | 273.69 | After cache clean |
| 2026-03-16 | 17:27 | 41c0ba6 | #2 | ✅ Pass | False | True | 56 | 229376 | 64 | 4 | 80.14 | 306.71 | After cache clean |
| 2026-03-16 | 17:27 | 41c0ba6 | #2 | ✅ Pass | True | False | 56 | 229376 | 64 | 4 | 98.72 | 194.58 | After cache clean |
| 2026-03-16 | 17:27 | 41c0ba6 | #2 | ✅ Pass | False | False | 56 | 229376 | 64 | 4 | 82.02 | 313.89 | After cache clean |
| 2026-03-16 | 18:02 | 41c0ba6 | #3 | ✅ Pass | True | True | 56 | 229376 | 64 | 4 | 138.89 | 273.75 | Reproduce run |
| 2026-03-16 | 18:02 | 41c0ba6 | #3 | ✅ Pass | False | True | 56 | 229376 | 64 | 4 | 80.15 | 306.73 | Reproduce run |
| 2026-03-16 | 18:02 | 41c0ba6 | #3 | ✅ Pass | True | False | 56 | 229376 | 64 | 4 | 98.71 | 194.56 | Reproduce run |
| 2026-03-16 | 18:02 | 41c0ba6 | #3 | ✅ Pass | False | False | 56 | 229376 | 64 | 4 | 82.03 | 313.93 | Reproduce run |

---

## Test Run Log

### Run #1 - 2026-03-16 16:51 (Initial Attempt)

**MTCC Commit:** `3c5851dee34cdf5523f556bab5dc7986278eb0bd`  
**MCC Version:** 5.1.0  
**Status:** ❌ **CRASH**

```
clang version 14.0.0 (git@sh-code.mthreads.com:sw/mtcc.git 3c5851dee34cdf5523f556bab5dc7986278eb0bd)
mcc version 5.1.0
```

**Error:**
```
RuntimeError: ninja exited with status 254
clang-14: /root/code/mtcc/llvmsrc/llvm/lib/CodeGen/RegisterPressure.cpp:1255: 
Assertion `(PDiffI->getUnitInc() >= 0) == (PNew >= POld) && "PSet overflow/underflow"' failed.
```

**Root Cause:** LLVM register pressure tracker assertion failure in Machine Instruction Scheduler pass.

---

### Run #2 - 2026-03-16 17:27 (After Cache Clean)

**MTCC Commit:** `41c0ba6bf76dfc34c4b10621705aa3293a5a1e5d`  
**MCC Version:** 5.1.0  
**Status:** ✅ **SUCCESS** (after `rm -rf ~/.cache/tvm-ffi`)

**Configuration:**
- `IsVarlenQ: True, IsVarlenKV: False, IsPagedKV: True`
- `MaxSeqlenQ: 4096, HeadDimQK: 128, HeadDimVO: 128`

**Results:**

| IsCausal | IsLocal | IsPackGQA | Bandwidth (GB/s) | Compute (TFLOPS) |
|----------|---------|-----------|------------------|------------------|
| True | False | True | 138.86 | 273.69 |
| False | False | True | 80.14 | 306.71 |
| True | False | False | 98.72 | 194.58 |
| False | False | False | 82.02 | 313.89 |

**Key Findings:**
- **Best Compute:** 313.89 TFLOPS (non-causal, no packGQA)
- **Best Bandwidth:** 138.86 GB/s (causal, packGQA)
- Cache clean workaround resolved the JIT crash from commit 3c5851dee

---

### Run #3 - 2026-03-16 18:02 (Reproduce Run)

**MTCC Commit:** `41c0ba6bf76dfc34c4b10621705aa3293a5a1e5d`  
**MCC Version:** 5.1.0  
**Status:** ✅ **SUCCESS** (reproduced after cache clean)

**Configuration:**
- `IsVarlenQ: True, IsVarlenKV: False, IsPagedKV: True`
- `MaxSeqlenQ: 4096, HeadDimQK: 128, HeadDimVO: 128`

**Results:**

| IsCausal | IsLocal | IsPackGQA | Bandwidth (GB/s) | Compute (TFLOPS) |
|----------|---------|-----------|------------------|------------------|
| True | False | True | 138.89 | 273.75 |
| False | False | True | 80.15 | 306.73 |
| True | False | False | 98.71 | 194.56 |
| False | False | False | 82.03 | 313.93 |

**Run-to-Run Variation (vs Run #2):**
- Bandwidth: ±0.03 GB/s (negligible)
- Compute: ±0.06 TFLOPS (negligible)
- Results are stable and reproducible ✅

---

## MCC Version History

| Date | Time | MTCC Commit | Run # | MCC Version | Status | Notes |
|------|------|-------------|-------|-------------|--------|-------|
| 2026-03-16 | 16:51 | 3c5851dee | #1 | 5.1.0 | ❌ Crash | LLVM assert in RegisterPressure.cpp |
| 2026-03-16 | 17:27 | 41c0ba6 | #2 | 5.1.0 | ✅ Pass | Cache clean workaround works; 4 configs tested |
| 2026-03-16 | 18:02 | 41c0ba6 | #3 | 5.1.0 | ✅ Pass | Reproduced Run #2; results stable (±0.03 GB/s, ±0.06 TFLOPS) |

---

## Known Issues

### LLVM Register Pressure Tracker Crash

**Affected Commit:** `3c5851dee34cdf5523f556bab5dc7986278eb0bd` (and possibly nearby commits)

**Symptom:** `Assertion '(PDiffI->getUnitInc() >= 0) == (PNew >= POld) && "PSet overflow/underflow"' failed`

**Location:** `/root/code/mtcc/llvmsrc/llvm/lib/CodeGen/RegisterPressure.cpp:1255`

**Trigger:** JIT compilation of flash attention metadata kernel

**Workaround:** Clear TVM-FFI cache before running:
```bash
rm -rf ~/.cache/tvm-ffi
MATE_FORCE_JIT=1 TVM_FFI_MUSA_ARCH_LIST=3.1 python benchmarks/bench_flash_attn.py
```

**Status:** Workaround confirmed working on commit 41c0ba6 (2026-03-16). Results are reproducible across multiple runs.

---

## Next Steps

- [ ] Report LLVM assert bug to MTCC team (commit 3c5851dee)
- [ ] Continue benchmarking on new commits
- [ ] Compare performance across commits
- [ ] Track when the crash is fixed in MTCC

---

**Tags:** #benchmark #flash-attn #mcc #jit #compiler-bug #performance #mtcc
