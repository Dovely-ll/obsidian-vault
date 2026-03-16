## Replicate
### Command:
```
/usr/local/musa/bin/mcc -MD -MF /home/devuser/workspace/mate/build/temp.linux-x86_64-cpython-310/csrc/mla_pybind.o.d -I/home/devuser/workspace/mate/3rdparty/mutlass/include -I/home/devuser/workspace/mate/3rdparty/mutlass/tools/util/include -I/home/devuser/workspace/mate/3rdparty/mutlass/experimental/fmha -I/home/devuser/workspace/mate/include -I/usr/local/musa/include -I/home/devuser/conda/envs/py310/lib/python3.10/site-packages/torch_musa/share/generated_cuda_compatible/aten/src -I/home/devuser/conda/envs/py310/lib/python3.10/site-packages/torch_musa/share/generated_cuda_compatible/include -I/home/devuser/conda/envs/py310/lib/python3.10/site-packages/torch_musa/share/generated_cuda_compatible/include/torch/csrc/api/include -I/home/devuser/conda/envs/py310/lib/python3.10/site-packages/torch_musa/share/torch_musa_codegen -I/home/devuser/conda/envs/py310/lib/python3.10/site-packages -I/usr/local/musa/include -I/home/devuser/conda/envs/py310/include/python3.10 -c -c /home/devuser/workspace/mate/csrc/mla_pybind.mu -o /home/devuser/workspace/mate/build/temp.linux-x86_64-cpython-310/csrc/mla_pybind.o -fPIC -Od3 -O2 -DNDEBUG -std=c++17 -fno-strict-aliasing -fno-signed-zeros -mllvm -mtgpu-load-cluster-mutation=1 -mllvm --num-dwords-of-load-in-mutation=64 --offload-arch=mp_31 -march=native -DTORCH_API_INCLUDE_EXTENSION_H '-DPYBIND11_COMPILER_TYPE="_gcc"' '-DPYBIND11_STDLIB="_libstdcpp"' '-DPYBIND11_BUILD_ABI="_cxxabi1016"' -DTORCH_EXTENSION_NAME=_C -D_GLIBCXX_USE_CXX11_ABI=1
```

## Debug Log
### Phenomenon:
- mcc编译到object file报错，分步编译可成功到.s

### Conclusion
 - Host编译报错，原因为之前对于寄存器压力的修改对host代码同样有影响