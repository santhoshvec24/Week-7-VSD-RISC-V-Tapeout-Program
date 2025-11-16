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


```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_floorplan
```

placement

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk place
```

Visualize Placement

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_place
```


Clock Tree Synthesis (CTS)
Run CTS
```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk cts
```


Visualize Clock Tree
```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_cts
```


Routing
Run Routing

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk route
```

```bash
cd OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base
```


Alternative manual generation:
```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk spef
```

SPEF File Structure
A typical SPEF file contains:

```
*SPEF "ieee 1481-1999"
*DESIGN "vsdbabysoc"
*DATE "16:13:34 Sunday November 16, 2025"
*VENDOR "The OpenROAD Project"
*PROGRAM "OpenROAD"
*VERSION "v2.0-22706-g9c4e436fd2"
*DESIGN_FLOW "NAME_SCOPE LOCAL" "PIN_CAP NONE"
*DIVIDER /
*DELIMITER :
*BUS_DELIMITER []
*T_UNIT 1 NS
*C_UNIT 1 PF
*R_UNIT 1 OHM
*L_UNIT 1 HENRY

*NAME_MAP
*1 CLK
*2 ENb_CP
*3 ENb_VCO
*4 net11
*5 REF
*6 RV_TO_DAC\[0\]
*7 RV_TO_DAC\[1\]
*8 RV_TO_DAC\[2\]
*9 RV_TO_DAC\[3\]
*10 RV_TO_DAC\[4\]
*11 RV_TO_DAC\[5\]
*12 RV_TO_DAC\[6\]
*13 RV_TO_DAC\[7\]
*14 RV_TO_DAC\[8\]
*15 RV_TO_DAC\[9\]
*16 VCO_IN
*CAP
1 *12834:D 0.000113333
2 *12997:LO 0.000113333
3 *12834:CLK *12834:D 0.000198052
4 *12964:D *12834:D 0.000119335
5 *6484:44 *12834:D 0.000200042
*RES
1 *12997:LO *12834:D 32.3611 
*END

*D_NET *6554 0.000834982
*CONN
*I *12837:D I *D sky130_fd_sc_hd__dfxtp_1
*I *12998:LO O *D sky130_fd_sc_hd__conb_1
*CAP
1 *12837:D 0.000233494
2 *12998:LO 0.000233494
3 *12837:CLK *12837:D 0.000367993
*RES
1 *12998:LO *12837:D 34.8526 
*END

*D_NET *6555 0.000337553
*CONN
*I *12839:D I *D sky130_fd_sc_hd__dfxtp_1
*I *12999:LO O *D sky130_fd_sc_hd__conb_1
*CAP
1 *12839:D 0.000118634
2 *12999:LO 0.000118634
3 *9521:A *12839:D 0.000100285
*RES
1 *12999:LO *12839:D 21.2773 
*END
```




SPEF Impact on Timing

Without SPEF (Pre-Route):
```
Net delay = 0.1 ns (estimated)
Total path delay = 5.5 ns
```

With SPEF (Post-Route):
```
Net delay = 0.3 ns (extracted: R=25Ω, C=12fF)
Total path delay = 5.7 ns
```


Verify SPEF Generation

# Check if SPEF file exists
```
ls -lh results/sky130hd/vsdbabysoc/base/6_final.spef
```

# View SPEF statistics
```
head -n 50 results/sky130hd/vsdbabysoc/base/6_final.spef
```

 Expected Output

```
-rw-r--r-- 1 user user 2.3M Nov 16 10:45 6_final.spef

```
 Using SPEF for Post-Route STA

# In OpenSTA or PrimeTime
```
read_spef results/sky130hd/vsdbabysoc/base/6_final.spef
report_timing -path_delay max
report_timing -path_delay min
```









