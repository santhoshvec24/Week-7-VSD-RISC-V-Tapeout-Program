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
export DESIGN_NICKNAME = vsdbabysoc
export DESIGN_NAME = vsdbabysoc
export PLATFORM    = sky130hd
export DESIGN_HOME = /home/vboxuser/OpenROAD-flow-scripts/flow/designs
# export VERILOG_FILES_BLACKBOX = $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/IPs/*.v
# export VERILOG_FILES = $(sort $(wildcard $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/*.v))
# Explicitly list the Verilog files for synthesis
export VERILOG_FILES = $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/vsdbabysoc.v \
                       $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/rvmyth.v \
                       $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/clk_gate.v

export SDC_FILE      = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)/vsdbabysoc_synthesis.sdc

export vsdbabysoc_DIR = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)

export VERILOG_INCLUDE_DIRS = $(wildcard $(vsdbabysoc_DIR)/include/)

export ADDITIONAL_GDS = $(wildcard $(vsdbabysoc_DIR)/gds/*.gds)
export ADDITIONAL_LEFS = $(wildcard $(vsdbabysoc_DIR)/lef/*.lef)
#export ADDITIONAL_LIBS = $(wildcard $(vsdbabysoc_DIR)/lib/*.lib)
# export PDN_TCL = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)/pdn.tcl


# Clock Configuration
#export CLOCK_PERIOD = 11.00
export CLOCK_PORT = CLK
export CLOCK_NET  = $(CLOCK_PORT)


# Pin Order and Macro Placement Configurations
export FP_PIN_ORDER_CFG = $(vsdbabysoc_DIR)/pin_order.cfg
export MACRO_PLACEMENT_CFG = $(vsdbabysoc_DIR)/macro.cfg

# Floorplanning Configuration
export DIE_AREA   = 0 0 1600 1600
export CORE_AREA  = 20 20 1590 1590

# Placement Configuration
export PLACE_PINS_ARGS = -exclude left:0-600 -exclude left:1000-1600 -exclude right:* -exclude top:* -exclude bottom:*

# Tuning for Timing and Buffers
export TNS_END_PERCENT     = 100
export REMOVE_ABC_BUFFERS  = 1

# CTS tuning
export CTS_BUF_DISTANCE = 600
export SKIP_GATE_CLONING = 1

# Magic Tool Configuration
export MAGIC_ZEROIZE_ORIGIN = 0
export MAGIC_EXT_USE_GDS    = 1

# export CORE_UTILIZATION=0.1  # Reduce this value to allow more whitespace for routing.
```

change the avsddac.lib as

```lib
library (avsddac) {
  time_unit : "1ns";
  voltage_unit : "1V";
  current_unit : "1uA";
  pulling_resistance_unit : "1kohm";
  leakage_power_unit : "1nW";
  capacitive_load_unit(1, pf);

  slew_lower_threshold_pct_fall : 20.000000000;
  slew_lower_threshold_pct_rise : 20.000000000;
  slew_upper_threshold_pct_fall :  80.00000000;
  slew_upper_threshold_pct_rise :  80.00000000;
  input_threshold_pct_fall : 50.000000000;
  input_threshold_pct_rise : 50.000000000;
  output_threshold_pct_fall : 50.000000000;
  output_threshold_pct_rise : 50.000000000;

  type (bus_9_0) {
    base_type : array;
    data_type : bit;
    bit_width : 10;
    bit_from : 9;
    bit_to : 0;
    downto : true;
  }

  cell (avsddac) {
    pin(OUT) {
      direction : output;
      capacitance : 0.001;
    }
    
	pin (VREFH) {
      direction : input;
      max_transition : 2.5;
      capacitance : 0.001;
    }

  pin (VREFL) {
      direction : input;
      max_transition : 2.5;
      capacitance : 0.001;
    }
    
pg_pin (VSSA) {
  voltage_name : VSSA;
  pg_type : primary_ground;
}

pg_pin (VDDA) {
  voltage_name : VDDA;
  pg_type : primary_power;
}
    
	bus (D) {
      bus_type : bus_9_0;
      direction : input;
      max_transition : 2.5;
      pin (D[0]) {
        capacitance : 0.001;
      }
      pin (D[1]) {
        capacitance : 0.001;
      }
      pin (D[2]) {
        capacitance : 0.001;
      }
      pin (D[3]) {
        capacitance : 0.001;
      }
      pin (D[4]) {
        capacitance : 0.001;
      }
      pin (D[5]) {
        capacitance : 0.001;
      }
      pin (D[6]) {
        capacitance : 0.001;
      }
      pin (D[7]) {
        capacitance : 0.001;
      }
      pin (D[8]) {
        capacitance : 0.001;
      }
      pin (D[9]) {
        capacitance : 0.001;
      }
    }
  }
}
```
and also avsdpll.lib

```lib
library (avsdpll) {
  time_unit : "1ns";
  voltage_unit : "1V";
  current_unit : "1uA";
  pulling_resistance_unit : "1kohm";
  leakage_power_unit : "1nW";
  capacitive_load_unit(1, pf);

  slew_lower_threshold_pct_fall : 20.000000000;
  slew_lower_threshold_pct_rise : 20.000000000;
  slew_upper_threshold_pct_fall :  80.00000000;
  slew_upper_threshold_pct_rise :  80.00000000;
  input_threshold_pct_fall : 50.000000000;
  input_threshold_pct_rise : 50.000000000;
  output_threshold_pct_fall : 50.000000000;
  output_threshold_pct_rise : 50.000000000;

  cell (avsdpll) {
    pin(CLK) {
      direction : output;
      capacitance : 0.001;
    }

    pin (VCO_IN) {
      direction : input;
      max_transition : 2.5;
      capacitance : 0.001;
    }

    pin (ENb_CP) {
      direction : input;
      max_transition : 2.5;
      capacitance : 0.001;
    }
    
	  pin (ENb_VCO) {
      direction : input;
      max_transition : 2.5;
      capacitance : 0.001;
    }

    pin (REF) {
      direction : input;
      max_transition : 2.5;
      capacitance : 0.001;
    }

pg_pin (GND) {
  voltage_name : GND;
  pg_type : primary_ground;
}

    //pin (GND#2) {
    //  direction : input;
    //  max_transition : 2.5;
    //  capacitance : 0.001;
    //}


pg_pin (VDD) {
  voltage_name : VDD;
  pg_type : primary_power;
}
    
    //pin (VDD#2) {
    //  direction : input;
    //  max_transition : 2.5;
    //  capacitance : 0.001;
    //}

    //pin (VDD#3) {
    //  direction : input;
    //  max_transition : 2.5;
    //  capacitance : 0.001;
    //}
  }
}
```
```bash

cd OpenROAD-flow-scripts/flow
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk clean_all
export OPENROAD_EXE=/usr/bin/openroad
export YOSYS_EXE=/home/vboxuser/oss-cad-suite/bin/yosys
export OPENSTA_EXE=/usr/bin/sta
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk synth
```
<img width="1302" height="759" alt="image" src="https://github.com/user-attachments/assets/1c0e02e1-15dc-45d4-afeb-90285ed305e7" />

```bash
gvim reports/sky130hd/vsdbabysoc/base/synth_stat.txt
```

<img width="1246" height="761" alt="image" src="https://github.com/user-attachments/assets/f8acbc8a-941b-4d2c-904e-13eddfc75cac" />

Synthesis netlist

```bash
gvim results/sky130hd/vsdbabysoc/base/1_2_yosys.v
```

<img width="1304" height="766" alt="image" src="https://github.com/user-attachments/assets/670d255d-cad9-4e8c-b8fb-f5f011504794" />

Synthesis Check

```bash
gvim reports/sky130hd/vsdbabysoc/base/synth_check.txt
```

<img width="1304" height="766" alt="image" src="https://github.com/user-attachments/assets/29853d99-3577-498e-a531-06bb08635a88" />

run floorplan
```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk floorplan
```

Floorplan Result (GUI)
```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_floorplan
```




