# MCC JIT Compiler Crash - bench_flash_attn

**Date:** 2026-03-16  
**Server:** 10.18.32.15 (mccxadmin)  
**Container:** mtcc-mate-perf  
**Path:** /home/devuser/yangfan/perf/mate

---

## Command

```bash
MATE_FORCE_JIT=1 TVM_FFI_MUSA_ARCH_LIST=3.1 python benchmarks/bench_flash_attn.py
```

---

## MCC Version

```
clang version 14.0.0 (git@sh-code.mthreads.com:sw/mtcc.git 3c5851dee34cdf5523f556bab5dc7986278eb0bd)
mcc version 5.1.0
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /usr/local/musa/bin
```

**MTCC Commit:** `3c5851dee34cdf5523f556bab5dc7986278eb0bd`

---

## Error

```
RuntimeError: ninja exited with status 254
```

**Root Cause:** clang-14 assertion failure in LLVM register pressure tracker

```
clang-14: /root/code/mtcc/llvmsrc/llvm/lib/CodeGen/RegisterPressure.cpp:1255: 
void llvm::RegPressureTracker::getUpwardPressureDelta(...): 
Assertion `(PDiffI->getUnitInc() >= 0) == (PNew >= POld) && "PSet overflow/underflow"' failed.
```

**Stack Trace Highlights:**
- #10 `llvm::RegPressureTracker::getUpwardPressureDelta`
- #11 `llvm::GenericScheduler::initCandidate`
- #14 `llvm::ScheduleDAGMILive::schedule`
- Machine Instruction Scheduler pass failed

---

## Analysis

This is a **compiler toolchain bug**, not a benchmark issue. The LLVM-based MCC compiler crashed during the Machine Instruction Scheduler pass while compiling the JIT kernel for flash attention.

The assertion failure suggests register pressure tracking overflow/underflow during scheduling.

---

## Possible Workarounds

1. **Clean TVM-FFI cache:**
   ```bash
   rm -rf ~/.cache/tvm-ffi
   MATE_FORCE_JIT=1 TVM_FFI_MUSA_ARCH_LIST=3.1 python benchmarks/bench_flash_attn.py
   ```

2. **Try different MUSA arch:**
   ```bash
   MATE_FORCE_JIT=1 TVM_FFI_MUSA_ARCH_LIST=3.0 python benchmarks/bench_flash_attn.py
   ```

3. **Use pre-built kernels (skip JIT):**
   ```bash
   python benchmarks/bench_flash_attn.py
   ```

---

## Next Steps

- [ ] Report to MTCC team (include full stack trace)
- [ ] Try workaround #1 (clean cache)
- [ ] Check if pre-built kernels work
- [ ] Track MTCC commit that fixes this

---

**Tags:** #bug #compiler #mcc #jit #flash-attn #benchmark
