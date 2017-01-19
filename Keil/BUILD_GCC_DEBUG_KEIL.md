# Build with GCC and debug with Keil uVision

This is a simple guide for how to build with GCC toolchain and debug with Keil uVision IDE.

The *NuMaker-PFM-NUC472* board (*NUMAKER_PFM_NUC472* target) is taken as an example for explanation.

## Hardware setup
1. Switch the *NuMaker-PFM-NUC472* board to **Debug** mode.
1. Connect the board to the host computer via USB.

## mbed command line
1. Guide GCC to generate uVision-compatible debug information by adding the `"-gdwarf-2"` flag in the JSON file `mbed-os/tools/profiles/debug.json`.
    
    <pre>
    "GCC_ARM": {
            "common": ["-c", "-Wall", "-Wextra",
                    "-Wno-unused-parameter", "-Wno-missing-field-initializers",
                    "-fmessage-length=0", "-fno-exceptions", "-fno-builtin",
                        "-ffunction-sections", "-fdata-sections", "-funsigned-char",
                    "-MMD", "-fno-delete-null-pointer-checks",
                    "-fomit-frame-pointer", "-O0", “-g3”, <b>"-gdwarf-2"</b>],
    </pre>
    
1. Build *your_program* through **mbed CLI** and you would get *your_program*.elf in the BUILD/NUMAKER_PFM_NUC472/GCC_ARM folder.
    ```
    mbed compile -m NUMAKER_PFM_NUC472 -t GCC_ARM --profile mbed-os/tools/profiles/debug.json
    ```

## Keil uVision IDE
1. Create a uVision project, e.g. *Debug_mbed* through the menu **Project** > **New Project**. Select *NUC400/NUC472HI8AE* (**TARGET DEPENDENT**) as target.
1. Select *Nuvoton Nu-Link Driver/NUC400* (**TARGET DEPENDENT**) as ICE driver through the menu **Debug**.
1. Copy *your_program*.elf built above to the uVision project folder **Debug_mbed/Objects**.
1. Replace *Debug_mbed* with *your_program*.elf through the menu **Debug** > **Name of Executable**.
1. Download *your_program*.elf through the  menu **Flash** > **Erase** and then **Flash** > **Download**.
1. Enter debug session through the menu **Debug** > **Start Debug Session**.
