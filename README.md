# Week-7-VSD-RISC-V-Tapeout-Program




## Installing and setting up ORFS
```bash
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
cd OpenROAD-flow-scripts
sudo ./setup.sh
```
photo

```bash
./build_openroad.sh --local
```
photo

Verify Installation

```bash
source ./env.sh
yosys -help
openroad -help
cd flow
make
```

photo

```bash
make gui_final
```
