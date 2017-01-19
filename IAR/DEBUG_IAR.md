# Debug mbed program with IAR EWARM

This document guides how to debug mbed program with IAR EWARM.

To debug mbed program with IAR EWARM, please follow the steps below.
The NuMaker-PFM-NUC472 board (NUMAKER_PFM_NUC472 target) is taken as an example for explanation.

1. Software requirement
    - **IAR Embedded Workbench for ARM** 7.x or afterwards
    - [Nu-Link Driver (IAR)](http://www.nuvoton.com/hq/products/microcontrollers/arm-cortex-m4-mcus/Software/?__locale=en&resourcePage=Y)

1. Export IAR EWARM project from **mbed Online Compiler** or **mbed CLI**. To export through mbed CLI, run the command below:
    
    `$mbed export -i iar -m NUMAKER_PFM_NUC472`
    
1. Open the exported IAR EWARM project and change configurations as below if they are not so configured:
    1. Set **Processor variant** to *Device* and choose *NUC400AE series (NUC442AE,NUC472AE)* through the **menu Project** > **Options** > **General Options** > **Target**.
    1. Set **FPU** to *VFPv4 single precision* through the menu **Project** > **Options** > **General Options** > **Target**.
    1. Set **Library** to *Full* through the menu **Project** > **Options** > **General Options** > **Library Configuration**.
    1. Set optimization **Level** to *None* through the menu **Project** > **Options** > **Runtime Checking** > **C/C++ Compiler** > **Optimizations**.
    1. Set **entry symbol** to *Reset_Handler* through the menu **Project** > **Options** > **Runtime Checking** > **Linker** > **Library**.
    1. Set debug **Driver** to *Third-Party Driver* through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Setup**.
    1. Check *Use flash loader(s)* through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Download**.
    1. Choose e.g. `C:\Program Files\Nuvoton Tools\Nu-Link_IAR\Nu-Link_IAR.dll` installed above as **IAR debugger driver plugin** through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Third-Party Driver**.
