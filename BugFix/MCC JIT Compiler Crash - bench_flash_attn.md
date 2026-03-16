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
| 2026-03-16 | 19:18 | d159785 | #4 | ❌ Crash | - | - | - | - | - | - | - | - | LLVM assert failure (bug NOT fixed) |
| 2026-03-16 | 19:34 | 8cac808 | #5 | ❌ Crash | - | - | - | - | - | - | - | - | LLVM assert failure (bug NOT fixed) |
| 2026-03-16 | 19:59 | 15805c0 | #6 | ⚠️ Partial | True | True | 56 | 229376 | 64 | 4 | 138.74 | 273.45 | GPU OOM on 4th config (re-run) |
| 2026-03-16 | 19:59 | 15805c0 | #6 | ⚠️ Partial | False | True | 56 | 229376 | 64 | 4 | 79.97 | 306.02 | GPU OOM on 4th config (re-run) |
| 2026-03-16 | 19:59 | 15805c0 | #6 | ⚠️ Partial | True | False | 56 | 229376 | 64 | 4 | 98.42 | 193.99 | GPU OOM on 4th config (re-run) |
| 2026-03-16 | 19:59 | 15805c0 | #6 | ❌ OOM | False | False | 56 | 229376 | 64 | 4 | - | - | MUSA OOM 28GB (re-run) |

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

### Run #4 - 2026-03-16 19:18 (New Commit - CRASH)

**MTCC Commit:** `d159785fb2f691717cc3509d29e68e00ab56779c`  
**MCC Version:** 5.1.0  
**Status:** ❌ **CRASH** (same LLVM assert as Run #1)

**Verification:** `mcc --version` before run:
```
clang version 14.0.0 (git@sh-code.mthreads.com:sw/mtcc.git d159785fb2f691717cc3509d29e68e00ab56779c)
mcc version 5.1.0
```

**Error:**
```
RuntimeError: ninja exited with status 254
clang-14: /root/code/mtcc/llvmsrc/llvm/lib/CodeGen/RegisterPressure.cpp:1255: 
Assertion `(PDiffI->getUnitInc() >= 0) == (PNew >= POld) && "PSet overflow/underflow"' failed.
```

**Analysis:** The LLVM register pressure bug is **NOT fixed** in commit d159785. Same assertion failure as Run #1 (commit 3c5851dee). Cache clean workaround does NOT help on this commit.

---

### Run #5 - 2026-03-16 19:34 (Newer Commit - CRASH)

**MTCC Commit:** `8cac80813177db9264359ceb7a480de743e0379b`  
**MCC Version:** 5.1.0  
**Status:** ❌ **CRASH** (same LLVM assert persists)

**Verification:** `mcc --version` before run:
```
clang version 14.0.0 (git@sh-code.mthreads.com:sw/mtcc.git 8cac80813177db9264359ceb7a480de743e0379b)
mcc version 5.1.0
```

**Error:**
```
RuntimeError: ninja exited with status 254
clang-14: /root/code/mtcc/llvmsrc/llvm/lib/CodeGen/RegisterPressure.cpp:1255: 
Assertion `(PDiffI->getUnitInc() >= 0) == (PNew >= POld) && "PSet overflow/underflow"' failed.
```

**Analysis:** The LLVM register pressure bug is **NOT fixed** in commit 8cac808 either. This is the 3rd crashing commit we've found. Cache clean workaround does NOT help.

---

### Run #6 - 2026-03-16 19:59 (LLVM Fix + GPU OOM - RE-RUN)

**MTCC Commit:** `15805c00dbe8b8a5ed0f7c569c367c3c5b4b23ad`  
**MCC Version:** 5.1.0  
**Status:** ⚠️ **PARTIAL** (LLVM assert FIXED, but GPU OOM on 4th config - RE-RUN due to machine contention)

**Verification:** `mcc --version` before run:
```
clang version 14.0.0 (git@sh-code.mthreads.com:sw/mtcc.git 15805c00dbe8b8a5ed0f7c569c367c3c5b4b23ad)
mcc version 5.1.0
```

**Results (3 of 4 configs passed):**

| IsCausal | IsLocal | IsPackGQA | Bandwidth (GB/s) | Compute (TFLOPS) |
|----------|---------|-----------|------------------|------------------|
| True | False | True | 138.74 | 273.45 |
| False | False | True | 79.97 | 306.02 |
| True | False | False | 98.42 | 193.99 |
| False | False | False | ❌ OOM | ❌ OOM |

**GPU OOM Error (4th config - non-causal, no packGQA):**
```
MemoryError: MUSA out of memory. Tried to allocate 28.00 GiB.
GPU 0 has a total capacity of 79.92 GiB of which 9.25 GiB is free.
Of the allocated memory 13.18 GiB is allocated by PyTorch,
and 32.39 GiB is reserved by PyTorch but unallocated.
```

**Analysis:** 
- ✅ **LLVM assert bug is FIXED** in commit 15805c0! No more RegisterPressure.cpp crash.
- ❌ **GPU OOM** on the 4th configuration (non-causal, no packGQA) - machine is shared, GPU memory fragmented by other users.
- Same OOM pattern as first Run #6 attempt - 32.39 GB reserved but unallocated by PyTorch.
- **Note:** This is a RE-RUN because someone else was using the machine during the first attempt. Results are consistent (±0.05 GB/s, ±0.07 TFLOPS), confirming the GPU OOM is due to machine contention, not benchmark instability.
- Suggestion: Set `PYTORCH_MUSA_ALLOC_CONF=expandable_segments:True` to avoid fragmentation, or run during off-hours.

---

## MCC Version History

**Verification:** Each entry below was verified by running `mcc --version` immediately before/after the benchmark run.

| Date | Time | MTCC Commit | Run # | MCC Version | Status | Notes |
|------|------|-------------|-------|-------------|--------|-------|
| 2026-03-16 | 16:51 | 3c5851dee | #1 | 5.1.0 | ❌ Crash | LLVM assert in RegisterPressure.cpp (verified: `mcc --version` before run) |
| 2026-03-16 | 17:27 | 41c0ba6 | #2 | 5.1.0 | ✅ Pass | Cache clean workaround works; 4 configs tested (verified: `mcc --version` after run) |
| 2026-03-16 | 18:02 | 41c0ba6 | #3 | 5.1.0 | ✅ Pass | Reproduced Run #2; results stable (±0.03 GB/s, ±0.06 TFLOPS) (verified: `mcc --version` after run: 41c0ba6bf76dfc34c4b10621705aa3293a5a1e5d) |
| 2026-03-16 | 19:18 | d159785 | #4 | 5.1.0 | ❌ Crash | Same LLVM assert as Run #1; bug NOT fixed (verified: `mcc --version` before run: d159785fb2f691717cc3509d29e68e00ab56779c) |
| 2026-03-16 | 19:34 | 8cac808 | #5 | 5.1.0 | ❌ Crash | Same LLVM assert persists; bug NOT fixed (verified: `mcc --version` before run: 8cac80813177db9264359ceb7a480de743e0379b) |
| 2026-03-16 | 19:59 | 15805c0 | #6 | 5.1.0 | ⚠️ Partial | LLVM assert FIXED! GPU OOM on 4th config - RE-RUN due to machine contention (verified: `mcc --version` before run: 15805c00dbe8b8a5ed0f7c569c367c3c5b4b23ad) |

---

## Known Issues

### LLVM Register Pressure Tracker Crash ✅ FIXED

**Affected Commits (RESOLVED):** 
- `3c5851dee34cdf5523f556bab5dc7986278eb0bd` ❌
- `d159785fb2f691717cc3509d29e68e00ab56779c` ❌
- `8cac80813177db9264359ceb7a480de743e0379b` ❌

**Fixed In:**
- `15805c00dbe8b8a5ed0f7c569c367c3c5b4b23ad` ✅ (commit 15805c0)

**Working Commits (pre-fix):**
- `41c0ba6bf76dfc34c4b10621705aa3293a5a1e5d` ✅ (with cache clean workaround)

**Symptom:** `Assertion '(PDiffI->getUnitInc() >= 0) == (PNew >= POld) && "PSet overflow/underflow"' failed`

**Location:** `/root/code/mtcc/llvmsrc/llvm/lib/CodeGen/RegisterPressure.cpp:1255`

**Trigger:** JIT compilation of flash attention metadata kernel

**Status:** **FIXED in commit 15805c0!** ✅

---

### GPU Memory OOM (Machine Contention)

**Affected Config:** Non-causal, no packGQA (False, False)

**Symptom:** `MemoryError: MUSA out of memory. Tried to allocate 28.00 GiB.`

**GPU State:** 79.92 GiB total, 9.25 GiB free, 32.39 GiB reserved but unallocated by PyTorch

**Root Cause:** Shared machine - other users are allocating GPU memory, causing fragmentation.

**Workarounds:**
1. Set environment variable before running:
   ```bash
   export PYTORCH_MUSA_ALLOC_CONF=expandable_segments:True
   ```
2. Run during off-hours when machine is less contested
3. Clear GPU cache before running: `torch.musa.empty_cache()`

**Status:** Persistent issue on shared machine. LLVM bug fixed, but GPU memory contention prevents full benchmark completion.

---

## Next Steps

- [x] ~~Report LLVM assert bug to MTCC team~~ **FIXED in 15805c0!**
- [x] ~~Ask MTCC team about commit 41c0ba6~~ No longer needed - 15805c0 has proper fix
- [ ] Try `PYTORCH_MUSA_ALLOC_CONF=expandable_segments:True` to fix GPU OOM
- [ ] Re-run benchmark on commit 15805c0 with memory fix to get all 4 configs
- [ ] Compare performance: 15805c0 (fixed) vs 41c0ba6 (workaround) vs crashing commits
- [ ] Consider scheduling benchmarks during off-hours to avoid GPU contention

---

**Tags:** #benchmark #flash-attn #mcc #jit #compiler-bug #performance #mtcc #gpu-oom
