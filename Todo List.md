Test cases:
- printf
- initpreprocessor加`__MTGPU__`控制
- atomic_load_nand能否使用
- check LSU atomic store -> done
-远程dev环境

ing:
- devtech constant calculation: TME_LD: Fixed
- ph1s LSU burst 16
- __no_signed_zeros__
- LMA atomic不支持fp
- post encoder isa update


Replicate:
  case Intrinsic::musa_atomic_add_gen_f_cta:

  case Intrinsic::musa_atomic_add_gen_f_sys:

  case Intrinsic::musa_atomic_add_gen_i_cta:

  case Intrinsic::musa_atomic_add_gen_i_sys:

  case Intrinsic::musa_atomic_and_gen_i_cta:

  case Intrinsic::musa_atomic_and_gen_i_sys:

  case Intrinsic::musa_atomic_cas_gen_i_cta:

  case Intrinsic::musa_atomic_cas_gen_i_sys:

  case Intrinsic::musa_atomic_dec_gen_i_cta:

  case Intrinsic::musa_atomic_dec_gen_i_sys:

  case Intrinsic::musa_atomic_inc_gen_i_cta:

  case Intrinsic::musa_atomic_inc_gen_i_sys:

  case Intrinsic::musa_atomic_max_gen_i_cta:

  case Intrinsic::musa_atomic_max_gen_i_sys:

  case Intrinsic::musa_atomic_min_gen_i_cta:

  case Intrinsic::musa_atomic_min_gen_i_sys:

  case Intrinsic::musa_atomic_or_gen_i_cta:

  case Intrinsic::musa_atomic_or_gen_i_sys:

  case Intrinsic::musa_atomic_exch_gen_i_cta:

  case Intrinsic::musa_atomic_exch_gen_i_sys:

  case Intrinsic::musa_atomic_xor_gen_i_cta:

  case Intrinsic::musa_atomic_xor_gen_i_sys: