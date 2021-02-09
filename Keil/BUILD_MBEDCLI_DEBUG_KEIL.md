# Build with Mbed CLI and debug with Keil uVision

This is a simple guide for how to build with Mbed CLI `ARM`/`ARMC6`/`GCC_ARM` toolchain and debug with Keil uVision IDE.

The trick comes mainly from the [page](http://www.keil.com/support/docs/2310.htm) and some complements are added for Mbed program development.

Though [Export Keil uVision project](https://os.mbed.com/docs/mbed-os/v5.15/tutorials/keil-uvision.html) can achieve more complete development/debugging purpose, this approach can be preferred in the following conditions:

1.  Sometimes a bug can only re-produce with the original executable built by Mbed CLI. An executable built from exported Keil project cannot re-produce the bug.
1.  Building with Mbed CLI can take much less time than building the exported Keil project in Keil uVision IDE.

The *NuMaker-PFM-NUC472* board (*NUMAKER_PFM_NUC472* target) is taken as an example for explanation.

## Hardware setup
1.  Switch the *NuMaker-PFM-NUC472* board to **Debug** mode.
1.  Connect the board to the host computer via USB.

## mbed command line
1.  Guide compiler to generate uVision-compatible debug information in the JSON file say `mbed-os/tools/profiles/develop.json` for **Develop** profile.

    **GNU Arm Embedded Toolchain**: 
    <pre>
    "GCC_ARM": {
        "common": ["-c", "-Wall", "-Wextra",
                   "-Wno-unused-parameter", "-Wno-missing-field-initializers",
                   "-fmessage-length=0", "-fno-exceptions", "-fno-builtin",
                   "-ffunction-sections", "-fdata-sections", "-funsigned-char",
                   "-MMD", "-fno-delete-null-pointer-checks",
                   "-fomit-frame-pointer", <b>"-O0"</b>, <b>"-g"</b>, <b>"-gdwarf-2"</b>, <b>"-DMBED_DEBUG"</b>],
    </pre>

    **Arm Compiler 6**: 
    <pre>
    "ARMC6": {
        "common": ["-c", "--target=arm-arm-none-eabi", "-mthumb", <b>"-O0"</b>, <b>"-g"</b>, <b>"-gdwarf-3"</b>, <b>"-DMBED_DEBUG"</b>,
                   "-Wno-armcc-pragma-push-pop", "-Wno-armcc-pragma-anon-unions",
                   "-DMULADDC_CANNOT_USE_R7", "-fdata-sections",
                   "-fno-exceptions", "-MMD", "-D_LIBCPP_EXTERN_TEMPLATE(...)="],
    </pre>

    **Arm Compiler 5**: 
    <pre>
    "ARM": {
        "common": ["-c", "--gnu", "-Otime", "--split_sections",
                   "--apcs=interwork", "--brief_diagnostics", "--restrict",
                   "--multibyte_chars", <b>"-O0"</b>, <b>"-g"</b>, <b>"-DMBED_DEBUG"</b>],
    </pre>

    1.  Changing optimization level to **"-O0"**
    1.  Changing debug information level to **"-g"**
    1.  Changing debug information format to:
        -   **"-gdwarf-2"** for GNU Arm Embedded Toolchain
        -   **"-gdwarf-3"** for Arm Compiler 6
    1.  Add **"-DMBED_DEBUG"** macro.
        Also confirm `platform.idle-thread-stack-size-debug-extra` is defined in `mbed-os/rtos/mbed_lib.json` (pre-Mbed OS 6) or `mbed-os/cmsis/device/rtos/mbed_lib.json` (Mbed OS 6).
        Both `MBED_DEBUG` and `platform.idle-thread-stack-size-debug-extra` must be ready to enable extra idle thread stack size in debug build.

    

    1.  With `O0`, you could meet thread stack overflow more easily. Recommend enlarging stack sizes of following threads:
        -   Enlarge kernel thread stack size to say `0x1000` to guarantee crash message dump can succeed.
            Kernel can detect thread stack overflow by default, but it may meet kernel stack overflow again during its crash message dump sequence.

            **mbed_app.json**:
            ```json
            "target_overrides": {
                "*": {
                    ......
                    "target.boot-stack-size": "0x1000",
                    ......
                },
            ```
        -   Enlarge shared event queue thead stack size to say `4096`. Its use is common by .e.g. WiFi ESP8266 driver.

            **mbed_app.json**:
            ```json
            "target_overrides": {
                "*": {
                    ......
                     "events.shared-stacksize": 4096,
                    ......
                },
            ```
        -   Enlarge main thread stack size dependent on real use case.

            **mbed_app.json**:
            ```json
            "target_overrides": {
                "*": {
                    ......
                    "rtos.main-thread-stack-size": 5120,
                    ......
                },
            ```

1.  Build *your_program* through **Mbed CLI** and you would get *your_program*.elf in the BUILD/NUMAKER_PFM_NUC472/ARM folder.
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
    