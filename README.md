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

The below given files should be in the respective directories

In the directory `~/OpenROAD-flow-scripts/flow/designs/sky130hd/vsdbabysoc` 
```requirements
designs/sky130hd/vsdbabysoc
├── config.mk
├── gds
│   ├── avsddac.gds
│   └── avsdpll.gds
├── include
│   ├── sandpiper_gen.vh
│   ├── sandpiper.vh
│   ├── sp_default.vh
│   └── sp_verilog.vh
├── lef
│   ├── avsddac.lef
│   └── avsdpll.lef
├── lib
│   ├── avsddac.lib
│   └── avsdpll.lib
├── macro.cfg
├── pin_order.cfg
└── vsdbabysoc_synthesis.sdc
```
in the `~/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc`

```requirements
vsdbabysoc
├── avsddac.v
├── avsdpll.v
├── clk_gate.v
├── macro.cfg
├── pin_order.cfg
├── rvmyth_gen.v
├── rvmyth.v
├── testbench.rvmyth.post-routing.v
├── testbench.v
└── vsdbabysoc.v
```
then config.mk in vsdbabysoc which we created should contains the following:
```mk
  # Design and Platform Configuration
   export DESIGN_NICKNAME = vsdbabysoc
   export DESIGN_NAME = vsdbabysoc
   export PLATFORM    = sky130hd

  # Design Paths
  export vsdbabysoc_DIR = /home/vboxuser/OpenROAD-flow-scripts/flow/designs/sky130hd/$(DESIGN_NICKNAME)

  # Explicitly list Verilog files for synthesis
   export VERILOG_FILES = /home/vboxuser/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/vsdbabysoc.v \
                         /home/vboxuser/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/rvmyth.v \
                         /home/vboxuser/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/clk_gate.v


  # Include Directory for Verilog Header Files
   export VERILOG_INCLUDE_DIRS = $(vsdbabysoc_DIR)/include

  # Constraints File
    export SDC_FILE = $(vsdbabysoc_DIR)/vsdbabysoc_synthesis.sdc

  # Additional GDS Files
    export ADDITIONAL_GDS = $(vsdbabysoc_DIR)/gds/avsddac.gds \
                            $(vsdbabysoc_DIR)/gds/avsdpll.gds

  # Additional LEF Files
   export ADDITIONAL_LEFS = $(vsdbabysoc_DIR)/lef/avsddac.lef \
                            $(vsdbabysoc_DIR)/lef/avsdpll.lef

  # Additional LIB Files
   export ADDITIONAL_LIBS = $(vsdbabysoc_DIR)/lib/avsddac.lib \
                            $(vsdbabysoc_DIR)/lib/avsdpll.lib

 # Pin Order and Macro Placement Configurations
   export FP_PIN_ORDER_CFG = $(vsdbabysoc_DIR)/pin_order.cfg
   export MACRO_PLACEMENT_CFG = $(vsdbabysoc_DIR)/macro.cfg

 # Clock Configuration
   export CLOCK_PORT = CLK
   export CLOCK_NET  = $(CLOCK_PORT)
   export CLOCK_PERIOD = 20.0

# Floorplanning Configuration
  export DIE_AREA   = 0 0 1600 1600
  export CORE_AREA  = 20 20 1590 1590

# Placement Configuration
  export PLACE_PINS_ARGS = -exclude left:0-600 -exclude left:1000-1600 -exclude right:* -exclude top:* -exclude bottom:*

# Tuning for Timing and Buffers
  export TNS_END_PERCENT     = 100
  export REMOVE_ABC_BUFFERS  = 1
  export CTS_BUF_DISTANCE    = 600
  export SKIP_GATE_CLONING   = 1

 # Magic Tool Configuration
   export MAGIC_ZEROIZE_ORIGIN = 0
   export MAGIC_EXT_USE_GDS    = 1
```

<img width="1302" height="759" alt="image" src="https://github.com/user-attachments/assets/1c0e02e1-15dc-45d4-afeb-90285ed305e7" />
