# RISCV-Pulpissimo-FPGA-Implementation-for-ZCU102

*Note:*
- *CMC clients may submit their questions through CMC's online support form to get timely response.*
- *Further development work (such as new applications or ips) relavent to RISC-V on zcu102 from the community is welcome to release through CMC's github.You may contribute back to this github repository by submitting a pull request.*
- *CMC's clients are welcome to release their open source projects through CMC's github if they are willing share it with the community. Contact CMC for details.*
##
This README provides instructions on how to setup the environment to compile the pulpissimo fpga platform and configure it on the Xilinx ZCU102 evaluation board. Pulpissimo is a 32-bit RI5CY (a RISC-V compatible core ) single-core System-on-a-Chip developed by the PULP team (Parallel processing Ultra-low Power platform).
For more information about Pulpissimo, please visit [Pulpissimo on github](https://github.com/pulp-platform/pulpissimo).
For more information about PULP, please visit [PULP web site](https://pulp-platform.org/). 

CMC ported the pulpissimo platform to the Xilinx ZCU102 evaluation board based on the FPGA implementations for other FPGA boards provided by the PULP team. And this development has been merged into the master branch of the pulpissimo github repository(https://github.com/pulp-platform/pulpissimo). Therefore, users can obtain the pulpissimo-zcu102 from either the Pulpissimo github repository or from this repository. The "hello world" application used for testing the pulpissimo-zcu102 is only available from this repository. 


## System Requirements

 **Host OS:** the Pulpissimo platform and related tools are tested on Ubuntu 16.04 by CMC. information on other supported Linux distribution can be found here: https://github.com/pulp-platform/pulp-builder/blob/master/README.md

 **JTAG programming cable:** it is required to download and debug an application on Pulpissimo platform. Digilent JTAG HS2 programming cable is used in this case

 **Software tools and environment:**
 - RISC-V toolchain: cross-compiling tools for creating applications. more information can be found here: https://github.com/pulp-platform/pulp-riscv-gnu-toolchain
 - Pulp SDK: runtime environment for creating application running on Pulpissimo platform. More information can be found here:https://github.com/pulp-platform/pulp-sdk
 - Pulpissimo platform package: the tools and source files required to compile the Pulpissimo platform. More information can be found here: https://github.com/pulp-platform/pulpissimo
 - Minicom: a terminal used for communication between the host and the Pulpissimo platform.

Please note: most of the instructions provided below are duplicated from the Pulp-platform GitHub pages. This README brings the instructions that are provided in different Pulp-platform GitHub pages into one place for a quick and convenient getting started with Pulpissimo for ZCU102. You are encouraged to visit Pulp-platform GitHub for more details. 

## Download CMC Pulpissimo FPGA Implementation for ZCU102
```
$git clone https://github.com/cmcmicrosystems/pulpissimo-zcu102.git
```
## Installing Linux dependency 
Please install the following dependency on your host Ubuntu Linux:

```
$sudo apt install git python3-pip python-pip gawk texinfo libgmp-dev libmpfr-dev libmpc-dev swig3.0 libjpeg-dev lsb-core doxygen python-sphinx sox graphicsmagick-libmagick-dev-compat libsdl2-dev libswitch-perl libftdi1-dev cmake scons libsndfile1-dev

$sudo pip3 install artifactory twisted prettytable sqlalchemy pyelftools openpyxl xlsxwriter pyyaml numpy configparser pyvcd

$sudo pip2 install configparser
```
Please note: the default gcc version should be 5. Other version might make the build failed.
 
## Installing RISCV toolchain
This section provides instructions on installing the RISC-V cross-compiling tools.

**Download the sources**
```
$git clone –recursive https://github.com/pulp-platform/pulp-riscv-gnu-toolchain
```
**Install prerequisites**
```
$sudo apt-get install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev
```
**Choose an installation path**
You may choose any accessible directory to host the RISC-V toolchain . For example, /opt/riscv is picked to install the toolchain in this example. Add “/opt/riscv/bin” to your PATH environment variable:
```
$export PATH=”/opt/riscv/bin:$PATH”
```
**Build and Install Newlib cross-compiler for Pulp**
```
$cd pulp-riscv-gnu-toolchain $ ./configure –prefix=/opt/riscv –with-arch=rv32imc –with-cmodel=medlow –enable-multilib
$make $./configure –prefix=/opt/riscv
$make
```
You should now have riscv-gcc tools installed under /opt/riscv.

## Installing pulp platform and Build Pulp-SDK

**Download pulpissimo platform**
```
$cd ~ 
$git clone https://github.com/pulp-platform/pulpissimo.git
```
**Build pulp-sdk**
```
$cd pulpissimo
$git clone https://github.com/pulp-platform/pulp-sdk.git
$cd pulp-sdk
$export PULP_RISCV_GCC_TOOLCHAIN=”/opt/riscv/bin”
```
Copy the zcu102.sh to ~/pulpissimo/pulp-sdk/configs/fpgas/puplissimo/ as following:
```
$cp ~/pulpissimo-zcu102/zcu102.sh ~/pulpissimo/pulp-sdk/configs/fpgas/pulpissimo/
```
**Select target and platform, and Build SDK**
```
$source configs/pulpissimo.sh
$source configs/fpgas/pulpissimo/zcu102.sh
$make all
```
## ZCU102 FPGA Implementation

**Copy relevant folder and files**
```
$cp ~/pulpissimo-zcu102/Makefile ~/pulpissimo/fpga    # this step will overwrite the exiting Makefile in the fpga folder
```
In order to generate the PULPissimo bitstream for a supported target FPGA board, first generate the necessary synthesis include scripts by starting the update-ips script in the pulpissimo root directory:
```
$cd ~/pulpissimo
$./update-ips
```
**Build bitstream for zcu102**
```
$cd fpga
$make zcu102
```
You should find the pulpissimo-zcu102.bit generated under the current directory.

## Program ZCU102 board
**Step one:** Connect the ZCU102 evaluation board to your host machine with a Micro-USB cable from the J2 connector (USB JTAG) on the ZCU102 board to a USB port on your host machine.
**Step two:** Set the board boot mode to JTAG boot (all four DIP switch of the switch SW6 set to on position) More details on how to setup the zcu102 board are provided in the ZCU102 Evaluation Board User Guise.
**Step three:** Program the ZCU102 with Hardware Manager. Invoke vivado Open Hardware Manager Open Target Program device Detailed instructions on how to use hardware manager are provided in Vivado Design Suite User Guide – programming and Debugging

## Build Hello World Application
Copy the “hello” application example to pulp-sdk.
```
$cp -r ~/pulpissimo-zcu102/hello ~/pulpissimo/pulp-sdk
```
In the pulp-sdk directory, issue the following commands: 
```
$source configs/pulpissimo.sh
$source configs/fpgas/pulpissimo/zcu102.sh
$make env
$source sourceme.sh
$cd hello
$make clean all
```
You should see the binary is generated under ./build/pulpissimo/test/test.

## Run Hello Application on ZCU102 FPGA

**Step one:** Connect the UART of the ZCU102 (J83) to a USB port on your host.
**Step two:** Connect Digilent JTAG-HS2 adapter from (J55) of the ZCU102 to a USB port on your host.
Please note the HS2 connector should be connected to the J55 pins with odd numbers (the top row of the J55).
**Step three:** Program the board with the pulpissimo-zcu102.bit following the instructions provided in the previous section
**Step four:** Open three terminals on your host.

In terminal 1, issue the following commands:
```
$ minicom -s
```
Set serial port to /dev/ttyUSB0 with baud rate of 115200.
Please note you might need to configure the serial port to a different device (for example /dev/ttyUSB1) depending on which USB port on the host side is connected to the UART of the board.

In terminal 2, issue the following commands:
```
$cd ~/pulpisimo/pulp-sdk
$./pkg/openocd/1.0/bin/openocd -f ~/pulpissimo/fpga/pulpissimo-zcu102/openocd-zcu102-digilent-jtag-hs2.cfg
```
In terminal 3, issue the following commands:
```
$riscv32-unknown-elf-gdb ~/pulpissimo/pulp-sdk/hello/build/pulpissimo/test/test
```
In gdb, run:
```
(gdb)target remote localhost:3333
(gdb)load
(gdb)b main
(gdb)list
(gdb)continue
```
You should see “hello world!” in terminal 1 at this point.
