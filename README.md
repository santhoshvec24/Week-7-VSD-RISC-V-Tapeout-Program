# Week-7-VSD-RISC-V-Tapeout-Program




```bash
cd ~/OpenROAD-flow-scripts/tools/OpenROAD
rm -rf build
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
```
Enable in VM settings:

- System → Acceleration
    - VT-x / AMD-V
    - Nested paging

- Display →
   -  3D Acceleration
   -  128 MB video RAM

This reduces UI freeze.

<img width="1243" height="737" alt="image" src="https://github.com/user-attachments/assets/0abadedc-a0a1-46eb-9144-069ab660a85e" />


