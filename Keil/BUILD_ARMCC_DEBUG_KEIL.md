# Build with Mbed CLI ARM/ARMC6 and debug with Keil uVision

This is a simple guide for how to build with Mbed CLI `ARM`/`ARMC6` toolchain and debug with Keil uVision IDE.

The trick comes mainly from the [page](http://www.keil.com/support/docs/2310.htm) and some complements are added for Mbed program development.

Though [Export Keil uVision project](https://os.mbed.com/docs/mbed-os/v5.15/tutorials/keil-uvision.html) can achieve more complete development/debugging purpose, this approach can be preferred in the following conditions:

1.  Sometimes a bug can only re-produce with the original executable built by Mbed CLI. An executable built from exported Keil project cannot re-produce the bug.
1.  Building with Mbed CLI can take much less time than building the exported Keil project in Keil uVision IDE.

The *NuMaker-PFM-NUC472* board (*NUMAKER_PFM_NUC472* target) is taken as an example for explanation.

## Hardware setup
1.  Switch the *NuMaker-PFM-NUC472* board to **Debug** mode.
1.  Connect the board to the host computer via USB.

## mbed command line
1.  Guide ARM Compiler to generate uVision-compatible debug information in the JSON file `mbed-os/tools/profiles/develop.json` by:
    1.  Changing optimization level to **"-O0"**
    1.  Changing debug information level to **"-g"**
    1.  Changing debug information format to **"-gdwarf-3"** for ARM Compiler 6
    1.  Add **"-DMBED_DEBUG"** macro.
        Also confirm `idle-thread-stack-size-debug-extra` is defined in `mbed-os/rtos/mbed_lib.json` for debug target. Both `MBED_DEBUG` and `idle-thread-stack-size-debug-extra` must be ready to enable extra idle thread stack size in debug build.

    <pre>
    "ARMC6": {
        "common": ["-c", "--target=arm-arm-none-eabi", "-mthumb", <b>"-O0"</b>, <b>"-g"</b>, <b>"-gdwarf-3"</b>, <b>"-DMBED_DEBUG"</b>,
                   "-Wno-armcc-pragma-push-pop", "-Wno-armcc-pragma-anon-unions",
                   "-DMULADDC_CANNOT_USE_R7", "-fdata-sections",
                   "-fno-exceptions", "-MMD", "-D_LIBCPP_EXTERN_TEMPLATE(...)="],
    </pre>
    
    <pre>
    "ARM": {
        "common": ["-c", "--gnu", "-Otime", "--split_sections",
                   "--apcs=interwork", "--brief_diagnostics", "--restrict",
                   "--multibyte_chars", <b>"-O0"</b>, <b>"-g"</b>, <b>"-DMBED_DEBUG"</b>],
    </pre>

1. Build *your_program* through **Mbed CLI** and you would get *your_program*.elf in the BUILD/NUMAKER_PFM_NUC472/ARM folder.
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

## Known issues

This approach is not officially provided by Arm Mbed.
It has the following known issues:

1.  Generated ELF file is not completely compatible with Keil uVision and can cause Keil uVision to crash on entering debugging.
1.  Continuging above, source mapping doesn't work sometimes. Though, it can be addressed by the manual bring-up means:
    1.  Find the address of the function you want to source-debug by checking MAP file. Take note of it.
    1.  Pop up context menu from Disassembly View.
    1.  Enter the address in [Show Disassembly at Address](show_disassembly_at_address.png)
    