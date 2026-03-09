# 现象
线程数 > 操作数位宽 -> hang

# Debug Log
## Inline ASM
```
[==========] Running 9 tests from 1 test suite.
[----------] Global test environment set-up.
[----------] 9 tests from atomicAnd_blockBlockTest
[ RUN      ] atomicAnd_blockBlockTest.Int_Config0
=== 测试 i ===
网格: (1, 1, 1)
块: (16, 1, 1)
线程数: 16, 数据类型位数: 32
初始值: 0xffffffff
最终结果: 0xffff0000
=== 验证 int ===
线程数: 16
最终值: 0xffff0000
✓ 最终值验证通过
✓ 所有旧值验证通过
atomicAnd_block(int) PASSED
[       OK ] atomicAnd_blockBlockTest.Int_Config0 (142 ms)
[ RUN      ] atomicAnd_blockBlockTest.Int_Config1
=== 测试 i ===
网格: (1, 1, 1)
块: (32, 1, 1)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
最终结果: 0x0
=== 验证 int ===
线程数: 32
最终值: 0x0
✓ 最终值验证通过
✓ 所有旧值验证通过
atomicAnd_block(int) PASSED
[       OK ] atomicAnd_blockBlockTest.Int_Config1 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.Int_Config2
=== 测试 i ===
网格: (1, 1, 1)
块: (64, 1, 1)
线程数: 64, 数据类型位数: 32
初始值: 0xffffffff
设备同步错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:245: Failure
Value of: testatomicAnd_blockBlock<int>(int_test_configs[2].first, int_test_configs[2].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config2 (203004 ms)
[ RUN      ] atomicAnd_blockBlockTest.Int_Config3
=== 测试 i ===
网格: (1, 1, 1)
块: (8, 4, 1)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:246: Failure
Value of: testatomicAnd_blockBlock<int>(int_test_configs[3].first, int_test_configs[3].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config3 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.Int_Config4
=== 测试 i ===
网格: (1, 1, 1)
块: (4, 4, 2)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:247: Failure
Value of: testatomicAnd_blockBlock<int>(int_test_configs[4].first, int_test_configs[4].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config4 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.UInt_Config0
=== 测试 j ===
网格: (1, 1, 1)
块: (8, 1, 1)
线程数: 8, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:263: Failure
Value of: testatomicAnd_blockBlock<unsigned int>(uint_test_configs[0].first, uint_test_configs[0].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config0 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.UInt_Config1
=== 测试 j ===
网格: (1, 1, 1)
块: (32, 1, 1)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:264: Failure
Value of: testatomicAnd_blockBlock<unsigned int>(uint_test_configs[1].first, uint_test_configs[1].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config1 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.UInt_Config3
=== 测试 j ===
网格: (1, 1, 1)
块: (16, 2, 1)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:266: Failure
Value of: testatomicAnd_blockBlock<unsigned int>(uint_test_configs[3].first, uint_test_configs[3].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config3 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.UInt_Config4
=== 测试 j ===
网格: (1, 1, 1)
块: (4, 4, 2)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:267: Failure
Value of: testatomicAnd_blockBlock<unsigned int>(uint_test_configs[4].first, uint_test_configs[4].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config4 (0 ms)
[----------] 9 tests from atomicAnd_blockBlockTest (203147 ms total)

[----------] Global test environment tear-down
[==========] 9 tests from 1 test suite ran. (203147 ms total)
[  PASSED  ] 2 tests.
[  FAILED  ] 7 tests, listed below:
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config2
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config3
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config4
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config0
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config1
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config3
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config4

 7 FAILED TESTS
```
## MUSA
```
[==========] Running 9 tests from 1 test suite.
[----------] Global test environment set-up.
[----------] 9 tests from atomicAnd_blockBlockTest
[ RUN      ] atomicAnd_blockBlockTest.Int_Config0
=== 测试 i ===
网格: (1, 1, 1)
块: (16, 1, 1)
线程数: 16, 数据类型位数: 32
初始值: 0xffffffff
最终结果: 0xffff0000
=== 验证 int ===
线程数: 16
最终值: 0xffff0000
✓ 最终值验证通过
✓ 所有旧值验证通过
atomicAnd_block(int) PASSED
[       OK ] atomicAnd_blockBlockTest.Int_Config0 (139 ms)
[ RUN      ] atomicAnd_blockBlockTest.Int_Config1
=== 测试 i ===
网格: (1, 1, 1)
块: (32, 1, 1)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
最终结果: 0x0
=== 验证 int ===
线程数: 32
最终值: 0x0
✓ 最终值验证通过
✓ 所有旧值验证通过
atomicAnd_block(int) PASSED
[       OK ] atomicAnd_blockBlockTest.Int_Config1 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.Int_Config2
=== 测试 i ===
网格: (1, 1, 1)
块: (64, 1, 1)
线程数: 64, 数据类型位数: 32
初始值: 0xffffffff
设备同步错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:245: Failure
Value of: testatomicAnd_blockBlock<int>(int_test_configs[2].first, int_test_configs[2].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config2 (203004 ms)
[ RUN      ] atomicAnd_blockBlockTest.Int_Config3
=== 测试 i ===
网格: (1, 1, 1)
块: (8, 4, 1)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:246: Failure
Value of: testatomicAnd_blockBlock<int>(int_test_configs[3].first, int_test_configs[3].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config3 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.Int_Config4
=== 测试 i ===
网格: (1, 1, 1)
块: (4, 4, 2)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:247: Failure
Value of: testatomicAnd_blockBlock<int>(int_test_configs[4].first, int_test_configs[4].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config4 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.UInt_Config0
=== 测试 j ===
网格: (1, 1, 1)
块: (8, 1, 1)
线程数: 8, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:263: Failure
Value of: testatomicAnd_blockBlock<unsigned int>(uint_test_configs[0].first, uint_test_configs[0].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config0 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.UInt_Config1
=== 测试 j ===
网格: (1, 1, 1)
块: (32, 1, 1)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:264: Failure
Value of: testatomicAnd_blockBlock<unsigned int>(uint_test_configs[1].first, uint_test_configs[1].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config1 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.UInt_Config3
=== 测试 j ===
网格: (1, 1, 1)
块: (16, 2, 1)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:266: Failure
Value of: testatomicAnd_blockBlock<unsigned int>(uint_test_configs[3].first, uint_test_configs[3].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config3 (0 ms)
[ RUN      ] atomicAnd_blockBlockTest.UInt_Config4
=== 测试 j ===
网格: (1, 1, 1)
块: (4, 4, 2)
线程数: 32, 数据类型位数: 32
初始值: 0xffffffff
内核执行错误: the launch timed out and was terminated
compatible_atomicAnd_block.cu:267: Failure
Value of: testatomicAnd_blockBlock<unsigned int>(uint_test_configs[4].first, uint_test_configs[4].second)
  Actual: false
Expected: true
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config4 (0 ms)
[----------] 9 tests from atomicAnd_blockBlockTest (203145 ms total)

[----------] Global test environment tear-down
[==========] 9 tests from 1 test suite ran. (203145 ms total)
[  PASSED  ] 2 tests.
[  FAILED  ] 7 tests, listed below:
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config2
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config3
[  FAILED  ] atomicAnd_blockBlockTest.Int_Config4
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config0
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config1
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config3
[  FAILED  ] atomicAnd_blockBlockTest.UInt_Config4

 7 FAILED TESTS
```