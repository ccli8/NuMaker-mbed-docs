# Debug Mbed program with Keil uVision

This is a simple guide for how to debug Mbed program with Keil uVision.

To debug Mbed program with Keil uVision, please follow the steps below.


## Hardware setup
1. Switch the Nuvoton Mbed Enabled board to **Debug** mode
    - [NuMaker-PFM-NUC472](https://developer.mbed.org/platforms/Nuvoton-NUC472/): De-short the **MASS** jumper near the board's USB socket.
    - [NuMaker-PFM-M453](https://developer.mbed.org/platforms/Nuvoton-M453/): De-short the **MASS** jumper near the board's USB socket.
    - NuMaker-PFM-M487: Push No.1/2/3 to ON and No.4 to non-ON of the 4-switch row near the board's USB socket.
1. Connect the board to the host computer via USB. If the switch above is correct, you **WON'T** see USB drive in the host computer.

## Software requirement
-   [Keil MDK](http://www2.keil.com/mdk5) 5 or afterwards 
-   [Keil Nu-Link debugger driver](https://github.com/OpenNuvoton/Nuvoton_Tools)

    This installation will:
    1.  Update Nuvoton device database in Keil.
    1.  Install the latest Nu-Link driver.

## Export Keil uVision project 

You could export Keil uVision project from **Mbed Online Compiler** or through **Mbed CLI**.
To export program for e.g. *NuMaker-PFM-NUC472* target through Mbed CLI, run the command below:    
`$mbed export -i uvision5 -m NUMAKER_PFM_NUC472`

## Check configurations in Keil uVision IDE
Open the exported Keil uVision project and change configurations as below if they are not so configured. Most options would have been OK.

1. Select target through the menu **Device**
    - NuMaker-PFM-NUC472: *Nuvoton/NUC472/NUC472HI8AE*
    - NuMaker-PFM-M453: *Nuvoton/M451/M453VG6AE*
    - NuMaker-PFM-M487: *Nuvoton/M480/M487JIDAE*
1. Select ICE driver through the menu **Debug**
    - NuMaker-PFM-NUC472: *Nuvoton Nu-Link Debugger/NUC400* or *NULink Debugger/NUC400*
    - NuMaker-PFM-M453: *Nuvoton Nu-Link Debugger/M451* or *NULink Debugger/M451*
    - NuMaker-PFM-M487: *Nuvoton Nu-Link Debugger/M480* or *NULink Debugger/M480*

Now, you can enter debug session after you build OK if everything goes well.
