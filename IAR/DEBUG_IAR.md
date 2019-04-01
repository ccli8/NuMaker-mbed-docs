# Debug mbed program with IAR EWARM

This is a simple guide for how to debug mbed program with IAR EWARM.

To debug mbed program with IAR EWARM, please follow the steps below.
The *NuMaker-PFM-NUC472* board (*NUMAKER_PFM_NUC472* target) is taken as an example for explanation.

## Hardware setup
1. Switch the *NuMaker-PFM-NUC472* board to **Debug** mode. See its [board page](https://os.mbed.com/platforms/Nuvoton-NUC472/) for how to switch between **Mass** mode and **Debug** mode.
1. Connect the board to the host computer via USB.

## Software requirement
- **IAR Embedded Workbench for ARM 8.32** or afterwards
- [Nu-Link Driver (IAR)](http://www.nuvoton.com/hq/products/microcontrollers/arm-cortex-m4-mcus/Software/?__locale=en&resourcePage=Y)
  This installation will:
  1. Update Nuvoton device database in IAR.
  1. Install the latest Nu-Link driver. By steps below, you can choose to use the latest or IAR built-in (since IAR8) Nu-Link driver.
- [Arm Mbed CLI](https://github.com/ARMmbed/mbed-cli#installing-mbed-cli) (option)

## Export IAR EWARM project

You could export IAR EWARM project from **Mbed Online Compiler** or through **Mbed CLI**.
To export through Mbed CLI, run the command below:    
`$mbed export -i iar -m NUMAKER_PFM_NUC472`

## Check configurations in IAR EWARM IDE after export (option)
Usually, exported IAR project is ready to use.
If you meet trouble in development in IAR, you need to check configurations in exported IAR project.
Open the project with IAR EWARM and change configurations as below if they are not so configured.

1. Set **Processor variant** to *Device* and choose *Nuvoton NUC400AE series (NUC442AE,NUC472AE)* through the menu **Project** > **Options** > **General Options** > **Target**.
1. Set **FPU** to *VFPv4 single precision* through the menu **Project** > **Options** > **General Options** > **Target**.
1. Set **Library** to *Full* through the menu **Project** > **Options** > **General Options** > **Library Configuration**.
1. Set optimization **Level** to *None* through the menu **Project** > **Options** > **Runtime Checking** > **C/C++ Compiler** > **Optimizations**.
1. Set **entry symbol** to *Reset_Handler* through the menu **Project** > **Options** > **Runtime Checking** > **Linker** > **Library**.
1. Set debug **Driver** to *Third-Party Driver* through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Setup** (Skip if you want to just use IAR8 built-in Nu-Link driver).
1. Check *Use flash loader(s)* through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Download**.
1. Choose e.g. `C:\Program Files (x86)\Nuvoton Tools\Nu-Link_IAR\Nu-Link_IAR.dll` installed above as **IAR debugger driver plugin** through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Third-Party Driver** (Skip if you want to just use IAR8 built-in Nu-Link driver).
