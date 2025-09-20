# RISC-V Reference Soc Tapeout Program
### Tools Installation
Detailed instructions for installing the required tools can be found here:

### System Requirements
  RAM : 6 GB RAM
  ROM : 50 GB HDD
  Distribution version : Ubuntu 20.04 or Higher
  Virtual CPU : 4vCPU


### TOOLS Installation:

Yosys
```
 $ sudo apt-get update
$ git clone https://github.com/YosysHQ/yosys.git
$ cd yosys
$ sudo apt install make               # If make is not installed
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make config-gcc
# Yosys build depends on a Git submodule called abc, which hasn't been initialized yet. You need to run the following command before running make
$ git submodule update --init --recursive
$ make 
$ sudo make install
``` 

iVerilog
```
$ sudo apt-get update
$ sudo apt-get install iverilog
```

gtkwave
```
 $ sudo apt-get update
$ sudo apt install gtkwave
```
![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/main/week_0/Task0/SnapShots/gtkwave.png)
magic
```
$ sudo apt-get install m4
$ sudo apt-get install tcsh
$ sudo apt-get install csh
$ sudo apt-get install libx11-dev
$ sudo apt-get install tcl-dev tk-dev
$ sudo apt-get install libcairo2-dev
$ sudo apt-get install mesa-common-dev libglu1-mesa-dev
$ sudo apt-get install libncurses-dev
git clone https://github.com/RTimothyEdwards/magic
cd magic
./configure
make
make install 
```



