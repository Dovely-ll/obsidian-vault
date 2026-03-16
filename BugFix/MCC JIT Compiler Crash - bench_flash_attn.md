# Flash Attention Benchmark - JIT Compiler Issue Tracker

**Tracking:** MCC JIT compiler crashes and benchmark results across MTCC commits  
**Server:** 10.18.32.15 (mccxadmin)  
**Container:** mtcc-mate-perf  
**Path:** /home/devuser/yangfan/perf/mate

---

## Test Command

```bash
MATE_FORCE_JIT=1 TVM_FFI_MUSA_ARCH_LIST=3.1 python benchmarks/bench_flash_attn.py
```

---

## Benchmark Results Summary

| Date | MTCC Commit | Status | IsCausal | IsLocal | IsPackGQA | BatchSize | TotalSeqlenQ | HeadQ | HeadKV | HeadDim | Bandwidth (GB/s) | Compute (TFLOPS) | Notes |
|------|-------------|--------|----------|---------|-----------|-----------|--------------|-------|--------|---------|------------------|------------------|-------|
| 2026-03-16 | 3c5851dee | ❌ Crash | - | - | - | - | - | - | - | - | - | - | LLVM assert failure |
| 2026-03-16 | 3c5851dee | ✅ Pass | True | False | True | 56 | 229376 | 64 | 4 | 128 | 138.86 | 273.69 | After cache clean |
| 2026-03-16 | 3c5851dee | ✅ Pass | False | False | True | 56 | 229376 | 64 | 4 | 128 | 80.14 | 306.71 | After cache clean |
| 2026-03-16 | 3c5851dee | ✅ Pass | True | False | False | 56 | 229376 | 64 | 4 | 128 | 98.72 | 194.58 | After cache clean |
| 2026-03-16 | 3c5851dee | ✅ Pass | False | False | False | 56 | 229376 | 64 | 4 | 128 | 82.02 | 313.89 | After cache clean |

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

**MTCC Commit:** `3c5851dee34cdf5523f556bab5dc7986278eb0bd`  
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
- Cache clean workaround resolved the JIT crash

---

## MCC Version History

| Date | MTCC Commit | MCC Version | Status | Notes |
|------|-------------|-------------|--------|-------|
| 2026-03-16 | 3c5851dee | 5.1.0 | ⚠️ Bug + Fixed | LLVM assert bug; cache clean workaround works |

---

## Known Issues

### LLVM Register Pressure Tracker Crash

**Symptom:** `Assertion '(PDiffI->getUnitInc() >= 0) == (PNew >= POld) && "PSet overflow/underflow"' failed`

**Location:** `/root/code/mtcc/llvmsrc/llvm/lib/CodeGen/RegisterPressure.cpp:1255`

**Trigger:** JIT compilation of flash attention metadata kernel

**Workaround:** Clear TVM-FFI cache before running:
```bash
rm -rf ~/.cache/tvm-ffi
MATE_FORCE_JIT=1 TVM_FFI_MUSA_ARCH_LIST=3.1 python benchmarks/bench_flash_attn.py
```

**Status:** Workaround confirmed working (2026-03-16)

---

## Next Steps

- [ ] Report LLVM assert bug to MTCC team
- [ ] Monitor future MTCC commits for fix
- [ ] Continue benchmarking on new commits
- [ ] Compare performance across commits once bug is fixed

---

**Tags:** #benchmark #flash-attn #mcc #jit #compiler-bug #performance #mtcc
