# Enable post-mortem debugging with Keil uVision

This document instructs how to enable post-mortem debugging with Keil uVision on a crashed device.

The *NuMaker-IoT-M487* board (*NUMAKER_IOT_M487* target) is taken as an example for explanation.

**Note**: Arm Mbed has an official solution [Post-mortem debugging with ARM mbed](https://os.mbed.com/blog/entry/Post-mortem-debugging-with-ARM-mbed/), but it is not supported on Nuvoton targets.

## Hardware (before device is running)
1.  Switch the *NuMaker-IoT-M487* board to **Debug** mode.
1.  Connect the board's Nu-Link/Nu-Link2 adapter to the host computer via USB.

**Note**: For post-mortem debugging, the state of the crashed device cannot be destroyed, that is, no reset.

## Keil uVision IDE

1.  Create a dummy uVision project, e.g. *debug-mbed* through the menu **Project** > **New Project**. Select *M480/M487JIDAE* (**TARGET DEPENDENT**) as target.
1.  Select *Nuvoton Nu-Link Driver/M480* (**TARGET DEPENDENT**) as ICE driver through the menu **Debug**.
1.  Continuing above, select *Under Reset* in **Reset Options/Connect**.
1.  Un-check **Load Application at Startup** and **Run to main()** through the menu **Debug**.
1.  Enter debug session through the menu **Debug** > **Start Debug Session**.
1.  Stop the device through the menu **Debug** > **Stop**. You can find PC in Disassembly View.
1.  In Command Window, load debug information, rather than code, of your running program to enable source-level debugging. Without this, you can only debug your code in Disassembly View.

    ```
    load "<path-to-axf or elf>\\<your-debug-enabled-program.axf or elf>" incremental nocode
    ```
