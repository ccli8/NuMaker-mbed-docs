# Build with ARMCC and debug with Keil uVision

This is a simple guide for how to build with ARMCC toolchain and debug with Keil uVision IDE.

At first glance, this is unnecessary because mbed tool already supports **Export Keil Project**. But it is still useful because:

1. Sometimes a bug can only re-produce with the original executable built by mbed CLI. An executable built from exported Keil project cannot re-produce the bug.
1. Building with mbed CLI can take much less time than building the exported Keil project in Keil uVision IDE.

The trick comes mainly from the [page](http://www.keil.com/support/docs/2310.htm) and some complements are added for mbed development.

The *NuMaker-PFM-NUC472* board (*NUMAKER_PFM_NUC472* target) is taken as an example for explanation.

## Hardware setup
1. Switch the *NuMaker-PFM-NUC472* board to **Debug** mode.
1. Connect the board to the host computer via USB.

## mbed command line
1. Guide ARM Compiler to generate uVision-compatible debug information in the JSON file `mbed-os/tools/profiles/develop.json` by:
    1. Changing optimization level to `"-O0"`
    1. Changing debug information level to `"-g"`
    1. Changing debug information format to `"-gdwarf-3"` for ARM Compiler 6
    
    <pre>
    "ARMC6": {
        "common": ["-c", "--target=arm-arm-none-eabi", "-mthumb", <b>"-O0"</b>, <b>"-g"</b>, <b>"-gdwarf-3"</b>
                   "-Wno-armcc-pragma-push-pop", "-Wno-armcc-pragma-anon-unions",
                   "-DMULADDC_CANNOT_USE_R7", "-fdata-sections",
                   "-fno-exceptions", "-MMD", "-D_LIBCPP_EXTERN_TEMPLATE(...)="],
    </pre>
    
    <pre>
    "ARM": {
        "common": ["-c", "--gnu", "-Otime", "--split_sections",
                   "--apcs=interwork", "--brief_diagnostics", "--restrict",
                   "--multibyte_chars", <b>"-O0"</b>, <b>"-g"</b>],
    </pre>

1. Build *your_program* through **mbed CLI** and you would get *your_program*.elf in the BUILD/NUMAKER_PFM_NUC472/ARM folder.
    ```
    mbed compile -m NUMAKER_PFM_NUC472 -t ARM
    ```

## Keil uVision IDE
1. Create a dummy uVision project, e.g. *debug-mbed* through the menu **Project** > **New Project**. Select *NUC400/NUC472HI8AE* (**TARGET DEPENDENT**) as target.
1. Select *Nuvoton Nu-Link Driver/NUC400* (**TARGET DEPENDENT**) as ICE driver through the menu **Debug**.
1. Copy *your_program*.elf built above to the uVision project folder **debug-mbed/Objects**.
1. Replace *debug-mbed* with *your_program*.elf through the menu **Output** > **Name of Executable**.
1. Download *your_program*.elf through the  menu **Flash** > **Erase** and then **Flash** > **Download**.
1. Enter debug session through the menu **Debug** > **Start Debug Session**.
