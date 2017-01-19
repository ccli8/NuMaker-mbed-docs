# Debug mbed program with IAR EWARM

This is a simple guide for how to debug mbed program with IAR EWARM.

To debug mbed program with IAR EWARM, please follow the steps below.
The *NuMaker-PFM-NUC472* board (*NUMAKER_PFM_NUC472* target) is taken as an example for explanation.

## Software requirement
- **IAR Embedded Workbench for ARM** 7.x or afterwards
- [Nu-Link Driver (IAR)](http://www.nuvoton.com/hq/products/microcontrollers/arm-cortex-m4-mcus/Software/?__locale=en&resourcePage=Y)

## Export IAR EWARM project 

You could export IAR EWARM project from **mbed Online Compiler** or through **mbed CLI**.
To export through mbed CLI, run the command below:    
`$mbed export -i iar -m NUMAKER_PFM_NUC472`
    
## Fix debug options manually
Open the exported *PROJECT.ewd* file with text editor and fix debug options as below:

1. Search for **OCDynDriverList** and replace *NULINK_ID* with *THIRDPARTY_ID*.

    ```
    <option>
        <name>OCDynDriverList</name>
        <state>THIRDPARTY_ID</state>
    </option>
    ```

1. Search for **CThirdPartyDriverDll** and replace *###Uninitialized###* with *C:\Program Files\Nuvoton Tools\Nu-Link_IAR\Nu-Link_IAR.dll* (**DEPENDENT ON WHERE NU-LINK DRIVER (IAR) IS INSTALLED**).

    ```
    <option>
        <name>CThirdPartyDriverDll</name>
        <state>C:\Program Files\Nuvoton Tools\Nu-Link_IAR\Nu-Link_IAR.dll</state>
    </option>
    ```

## Check configurations in IAR EWARM IDE
Open the exported IAR EWARM project with IAR EWARM and change configurations as below if they are not so configured. Most options have been OK.

1. Set **Processor variant** to *Device* and choose *NUC400AE series (NUC442AE,NUC472AE)* through the **menu Project** > **Options** > **General Options** > **Target**.
1. Set **FPU** to *VFPv4 single precision* through the menu **Project** > **Options** > **General Options** > **Target**.
1. Set **Library** to *Full* through the menu **Project** > **Options** > **General Options** > **Library Configuration**.
1. Set optimization **Level** to *None* through the menu **Project** > **Options** > **Runtime Checking** > **C/C++ Compiler** > **Optimizations**.
1. Set **entry symbol** to *Reset_Handler* through the menu **Project** > **Options** > **Runtime Checking** > **Linker** > **Library**.
1. Set debug **Driver** to *Third-Party Driver* through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Setup**.
1. Check *Use flash loader(s)* through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Download**.
1. Choose e.g. `C:\Program Files\Nuvoton Tools\Nu-Link_IAR\Nu-Link_IAR.dll` installed above as **IAR debugger driver plugin** through the menu **Project** > **Options** > **Runtime Checking** > **Debugger** > **Third-Party Driver**.
