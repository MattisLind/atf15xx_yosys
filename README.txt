Experimental Yosys Toolchain for Microchip ATF15xx CPLDs
--------------------------------------------------------

--------------------------------------------------------
Quick Start Guide - Linux
--------------------------------------------------------

1. Install wine

sudo apt-get install wine-stable winetricks

2. Download and install the latest nightly of the Yosys Open-Source CAD Suite

https://github.com/YosysHQ/oss-cad-suite-build/releases

cd $HOME
wget https://github.com/YosysHQ/oss-cad-suite-build/releases/download/2022-11-22/oss-cad-suite-linux-x64-20221122.tgz
tar xf oss-cad-suite-linux-x64-20221122.tgz
rm -f oss-cad-suite-linux-x64-20221122.tgz
export PATH=~/oss-cad-suite/bin:$PATH

3. Checkout the atf15xx_yosys project

cd $HOME
git clone https://github.com/hoglet67/atf15xx_yosys.git

4. Copy the Atmel fitter binaries in $HOME/atf15xx_yosys/vendor/

These need to be extracted from the Microchip ProChip Designer download:

http://ww1.microchip.com/downloads/en/DeviceDoc/ProChip5.0.1.zip

Either install ProChip Designer on a Windows machine, or extract the files locally using wine:

cd $HOME
wget http://ww1.microchip.com/downloads/en/DeviceDoc/ProChip5.0.1.zip
unzip ProChip5.0.1.zip
WINEPREFIX=$HOME/.wine32 WINEARCH=win32 winetricks vcrun6sp6
WINEPREFIX=$HOME/.wine32 WINEARCH=win32 wine ProChip5_setup.exe
cp $HOME/.wine32/drive_c/ATMEL_PLS_Tools/Prochip/pldfit/{atmel.std,aprim.lib,fit150?.exe} $HOME/atf15xx_yosys/vendor/

These are the sha1sums of the files you should end up with in vendor/
4ea7f7c5696990d84b587f4145ae0625ae9116eb  aprim.lib
7e7010a44455e01e1db6a1f18f320823dcfb5d61  atmel.std
fbcd61cd5348b05000ccad3d0e08cf8a8fb94538  fit1502.exe
e99b14f68fa5de131aa388503535c51ecdf31295  fit1504.exe
5f1ef6d220466dff8ac031acbce4e7a55dfed153  fit1508.exe

5. Try one of the examples

cd $HOME/atf15xx_yosys/examples/mmu

Run Yosys:

../../run_yosys.sh mmu

This should create mmu.edif

Run the Fitter:

../../run_fitter.sh mmu

This creates several files, the most important being:

mmu.fit  - the fitter report
mmu.vo   - a gate-level verilog for timing simulation
mmu.jed  - a JEDEC file for programming with Atmisp


--------------------------------------------------------
Quick Start Guide - Windows
--------------------------------------------------------

1. Download and install the latest nightly of the Yosys Open-Source CAD Suite

https://github.com/YosysHQ/oss-cad-suite-build/releases

Extract it to a location of your choosing

2. Checkout the atf15xx_yosys project

git clone https://github.com/hoglet67/atf15xx_yosys.git

3. Install Microchip ProChip Designer

Download the compressed installer from
http://ww1.microchip.com/downloads/en/DeviceDoc/ProChip5.0.1.zip

Extract it and run the installation with defaults.

4. Open a command prompt

Either open a new command prompt and set the environment variable OSS_CAD_SUITE_PATH
to the location you extracted the Yosys Open-Source CAD Suite in step 1, or launch
start.bat from the location you installed it to.

5. Try one of the examples

cd atf15xx_yosys\examples\mmu

Run Yosys:

..\..\run_yosys.cmd mmu

This should create mmu.edif

Run the Fitter:

..\..\run_fitter.cmd mmu

This creates several files, the most important being:

mmu.fit  - the fitter report
mmu.vo   - a gate-level verilog for timing simulation
mmu.jed  - a JEDEC file for programming with Atmisp


--------------------------------------------------------
KNOWN ISSUES
--------------------------------------------------------

The Yosys EDIF output deliberately reverses the bit-order of multi-bit
ports, as this is what is expected by Vivado. Unfortunately it's not
what is expected by the Atmel fitter.

There is a open issue for this:
- EDIF: add support for different flavors of bit indexing
- https://github.com/YosysHQ/yosys/issues/568

Update: June 2023 - As of 0.30+21 yosys adds write_edif -lsbidx which
we now make use of in the run_yosys script. For earlier versions of
yosys we use some perl to modify the EDIF file to the same effect.
