## Things to check:
	How is MatchTable generated? Accoring to what? Why?
	How do parameters of CheckComplexPat originate?
	How do AMD and NV deal with Atomic Load/Store?

## Progress:
### Instruction List
#### Atomic LD/ST
`ISD::ATOMIC_LOAD`
`ISD::ATOMIC_STORE`
#### AtomicRMW
`ISD::ATOMIC_SWAP`
`ISD::ATOMIC_LOAD_ADD`
`ISD::ATOMIC_LOAD_SUB`
`ISD::ATOMIC_LOAD_AND`
==`ISD::ATOMIC_LOAD_NAND`==
`ISD::ATOMIC_LOAD_OR`
`ISD::ATOMIC_LOAD_XOR`
`ISD::ATOMIC_LOAD_MAX`
`ISD::ATOMIC_LOAD_MIN`
`ISD::ATOMIC_LOAD_UMAX`
`ISD::ATOMIC_LOAD_UMIN`
`ISD::ATOMIC_LOAD_FADD`
==`ISD::ATOMIC_LOAD_FSUB`==
#### AtomicCmpXchg
`ISD::ATOMIC_CMP_SWAP`
`ISD::ATOMIC_CMP_SWAP_WITH_SUCCESS`
### Operand Type
#### MTGPU LMA Atomic Operations and Supported Types

|        | B8  | U16 | U32 | S32 | U64 | S64 | B128 |
| ------ | :-: | :-: | :-: | :-: | :-: | :-: | :--: |
| `ADD`  |     |  P  |  P  |  T  |  T  |  P  |  P   |
| `SUB`  |     |  P  |  P  |  T  |  P  |  P  |  P   |
| `EXCH` |     |  P  |  P  |  T  |  T  |  P  |  P   |
| `MIN`  |     |  P  |  -  |  T  |  -  |  T  |      |
| `UMIN` |     |  P  |  T  |  -  |  T  |  -  |      |
| `MAX`  |     |  P  |  -  |  T  |  -  |  T  |      |
| `UMAX` |     |  P  |  T  |  -  |  T  |  -  |      |
| `INC`  |     |  P  |  T  |  P  |  P  |  P  |  P   |
| `DEC`  |     |  P  |  T  |  P  |  P  |  P  |  P   |
| `AND`  |     |  P  |  P  |  T  |  T  |  P  |  P   |
| `OR`   |     |  P  |  P  |  T  |  T  |  P  |  P   |
| `XOR`  |     |  P  |  P  |  T  |  T  |  P  |  P   |
| `CAS`  |     |  T  |  P  |  T  |  T  |  P  |  P   |


### Operand Sequence

|                                        | Instruction Def   | DAG                      | CheckComplexPat                                             |
| -------------------------------------- | ----------------- | ------------------------ | ----------------------------------------------------------- |
| `AtomicLoad`<br>`LoadInst`             | `[ptr]`           | `[chain, ptr]`           | `[$dst, [$index $addrStride + $immOffset], $burstStride]`   |
| `AtomicStore`<br>`StoreInst`           | `[val, ptr]`      | `[chain, ptr, val]`      | `[$data, [$index $addrStride +$immOffset], $burstStride]`   |
| `AtomicRMW`<br>`AtomicRMWInst`         | `[ptr, val]`      | `[chain, ptr, val]`      | `[$dst, [$index $addrStride +$immOffset], $data]`           |
| `AtomicCmpXchg`<br>`AtomicCmpXchgInst` | `[ptr, cmp, val]` | `[chain, ptr, cmp, swp]` | `[$dst, [$index $addrStride +$immOffset], $dataCmp, $data]` |
