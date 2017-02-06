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
1. Fix breakpoints cannot set in most source files by removing `".\"` from source files in relative path names.
    
    In mbed-os/tools/toolchains/arm.py,
    <pre>
    def compile(self, cc, source, object, includes):
    # Build compile command
    cmd = cc + self.get_compile_options(self.get_symbols(), includes)
    
    cmd.extend(self.get_dep_option(object))
    <b>
    # Remove '.\' from relative path names of source files. For example, '.\mbed-os\platform\retarget.cpp' will truncate to 'mbed-os\platform\retarget.cpp'.
    if source.startswith('.\\'):
        source = source[2:]
    </b>     
    cmd.extend(["-o", object, source])
    </pre>
    
    It seems Keil cannot handle well relative path names starting with `".\"` in line to address mapping. The workaround can avoid it.
    
1. Build *your_program* through **mbed CLI** and you would get *your_program*.elf in the BUILD/NUMAKER_PFM_NUC472/ARM folder.
    ```
    mbed compile -m NUMAKER_PFM_NUC472 -t ARM --profile mbed-os/tools/profiles/debug.json
    ```

## Keil uVision IDE
1. Create a dummy uVision project, e.g. *debug-mbed* through the menu **Project** > **New Project**. Select *NUC400/NUC472HI8AE* (**TARGET DEPENDENT**) as target.
1. Select *Nuvoton Nu-Link Driver/NUC400* (**TARGET DEPENDENT**) as ICE driver through the menu **Debug**.
1. Copy *your_program*.elf built above to the uVision project folder **debug-mbed/Objects**.
1. Replace *debug-mbed* with *your_program*.elf through the menu **Debug** > **Name of Executable**.
1. Download *your_program*.elf through the  menu **Flash** > **Erase** and then **Flash** > **Download**.
1. Enter debug session through the menu **Debug** > **Start Debug Session**.
