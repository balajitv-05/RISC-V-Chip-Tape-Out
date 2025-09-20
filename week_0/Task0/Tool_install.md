# RISC-V Reference Soc Tapeout Program
### Tools Installation
Detailed instructions for installing the required tools can be found here:

### System Requirements
  * RAM : 6 GB RAM
  * ROM : 50 GB HDD
  * Distribution version : Ubuntu 20.04 or Higher
  * Virtual CPU : 4vCPU


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
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/138ea9011e08e6114ccb666fc29554148dae0a75/week_0/Task0/SnapShots/yosys.png)

iVerilog
```
$ sudo apt-get update
$ sudo apt-get install iverilog
```
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/2733dc6b103ebd58d343755518e0fb45434faf1a/week_0/Task0/SnapShots/iverilog.png)

gtkwave
```
 $ sudo apt-get update
$ sudo apt install gtkwave
```
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/138ea9011e08e6114ccb666fc29554148dae0a75/week_0/Task0/SnapShots/gtkwave.png)

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
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/138ea9011e08e6114ccb666fc29554148dae0a75/week_0/Task0/SnapShots/magic.png)

netgen
```
tar -xvzf netgen-1.5.134.tgz
cd netgen-1.5.134
#git clone git://opencircuitdesign.com/netgen-1.5 
#cd netgen-1.5/
sudo ./configure 
sudo make
sudo make install
```
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/138ea9011e08e6114ccb666fc29554148dae0a75/week_0/Task0/SnapShots/magictkcon.png)

OpenSTA

```
git clone https://github.com/The-OpenROAD-Project/OpenSTA.git
cd OpenSTA
mkdir build
cd build
cmake ..
make
```
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/138ea9011e08e6114ccb666fc29554148dae0a75/week_0/Task0/SnapShots/opensta.png)


qrouter
```
tar -xvzf qrouter-1.4.59.tgz
cd qrouter-1.4.59
#git clone git://opencircuitdesign.com/qrouter-1.4 
#cd qrouter-1.4/
sudo ./configure 
sudo make
sudo make install
```
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/138ea9011e08e6114ccb666fc29554148dae0a75/week_0/Task0/SnapShots/qrouter.png)

ngspice
   After downloading the tarball from (https://sourceforge.net/projects/ngspice/files/) to a local
directory, unpack it using:
```
$ tar -zxvf ngspice-37.tar.gz
$ cd ngspice-37
$ mkdir release
$ cd release
$ ../configure --with-x --with-readline=yes --disable-debug
$ make
$ sudo make install
```
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/138ea9011e08e6114ccb666fc29554148dae0a75/week_0/Task0/SnapShots/ngspice.png)




