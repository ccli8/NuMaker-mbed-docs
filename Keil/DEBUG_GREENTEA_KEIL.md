# Debug Greentea test sample with Keil uVision

This is a simple guide for how to debug Greentea test sample with Keil uVision IDE.
This would be helpful to debug failed Greentea tests on new chip porting.

The guide combines the trick of manually running Greentea program [htrun](https://github.com/ARMmbed/htrun) and
the [debug trick with Keil uVision IDE](BUILD_ARMCC_DEBUG_KEIL.md).

The *NuMaker-PFM-NUC472* board (*NUMAKER_PFM_NUC472* target) and the Greentea test sample *mbed-os-tests-integration-basic*
are taken as example target and example test sample for explanation.

## Hardware setup
1. Switch the *NuMaker-PFM-NUC472* board to **Debug** mode.
1. Connect the board to the host computer via USB.

## Software requirement
- [Greentea test tool](https://github.com/ARMmbed/greentea)

## Steps
1. Build Greentea test sample *mbed-os-tests-integration-basic* of debug version
    1. Support debug build by following the [instructions](BUILD_ARMCC_DEBUG_KEIL.md).
    1. Build the Greentea test sample *mbed-os-tests-integration-basic* but not run it.
        
        ```
        mbed test -t ARM -m NUMAKER_PFM_NUC472 -n mbed-os-tests-integration-basic --compile --profile mbed-os/tools/profiles/debug.json
        ```
    
1. Debug with Keil uVision IDE
    1. Load the built sample *mbed-os-tests-integration-basic.elf* by following the [instructions](BUILD_ARMCC_DEBUG_KEIL.md).
    1. Free-run through the menu **Debug** > **Go**, and the test sample would wait for host program **mbedhtrun** to communicate with it.
    
1. On host computer, manually run **mbedhtrun** to communicate with the test sample, where *COM38* must be replaced to match your environment.
    ```
    mbedhtrun -p COM38:9600 --skip-flashing --skip-reset
    ```
