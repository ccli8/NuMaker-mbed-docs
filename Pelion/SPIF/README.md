
# Change storage from SD card to SPI flash for Pelion example #

The default storage for key and candidate firmware is set to MicroSD card in the Pelion example code Nuvoton version. The example has two parts, one is bootloader and the other one is demonstration code itself. Each part has its configuration, so both configuration files have to be modified for storage change.

The configuration is set to 2MiB SPI flash to compliant M487KMCAN. The 2MiB configuration is fine for NuMaker-IoT-M487 which has an larger external SPI flash on board.

The bootloader configuration parameters on [Mbed document pages](https://os.mbed.com/docs/mbed-os/latest/program-setup/bootloader-configuration.html).

The memory layout of NuMaker-IoT-M487 flash memories are as follows


## M487 internal flash memory layout ##
```
+--------------------------+ <-+ (E) (0x80000)
|                          |
|                          |
|                          |
|        Active App        |
|                          |
|                          |
|                          |
+--------------------------+ <-+ (D) (0x18400)
|                          |
|Active App Metadata Header|	 MBED_BOOTLOADER_ACTIVE_HEADER_REGION_SIZE  
|         (1KiB)           |    
+--------------------------+ <-+ (C) (0x18000)
|                          |
|         KVSTORE          |     APP_KVSTORE_SIZE, storage_filesystem.rbp_internal_size (MBED_CONF_STORAGE_FILESYSTEM_RBP_INTERNAL_SIZE)
|         (32KiB)          |
+--------------------------+ <-+ (B) (0x10000)
|                          |
|        Bootloader        |     MBED_BOOTLOADER_SIZE, mbed-bootloader.bootloader-size, bootloader-size
|         (64KiB)          |
+--------------------------+ <-+ (A) (0x0)

(E) End of internal flash address
		(MBED_ROM_START + MBED_ROM_SIZE)

(D) Base address of App
		target.app_offset
		APPLICATION_ADDR
		mbed-bootloader.application-start-address (MBED_CONF_MBED_BOOTLOADER_APPLICATION_START_ADDRESS)

(C) Base address of App Metadata Header
		target.header_offset
		update-client.application-details (MBED_CONF_UPDATE_CLIENT_APPLICATION_DETAILS)
		
(B) Base address of internal KVStore
		storage_tdb_internal.internal_base_address
		storage_filesystem.internal_base_address (MBED_CONF_STORAGE_FILESYSTEM_INTERNAL_BASE_ADDRESS)
		
(A) Base address of internal flash memory (Start of Bootloader)
		MBED_ROM_START
```		

## M487 external flash memory layout ##

Only reserved one copy of Candidate Firmware in this configuration.
```
+--------------------------+ <-+ (J) (0x200000)
|                          |
+--------------------------+ <-+ (I) 
|                          |
+- - - - - - - - - - - - - +
|                          |	 update-client.storage-size ==> (E)-(D) = 0x67C00
|   Firmware Candidate 0   |
|                          |
+--------------------------+ <-+ (H)
|   Firmware Candidate 0   |
|     Metadata Header      |
+--------------------------+ <-+ (G) (0x100000)
|                          |
|   External KVStore       |     storage_filesystem.external_size (MBED_CONF_STORAGE_FILESYSTEM_EXTERNAL_SIZE)
|         (1MiB)           |
|                          |
+--------------------------+ <-+ (F) (0x0)

(J) End of external SPI Flash
(I) End of reserved area for Candidate Firmware		
(H) Base address of Candidate Firmware
(G) Base address of Candidate Firmware Metadata Header
		update-client.storage-address
		
(F) Base address of external flash memory
		storage_filesystem.external_base_address (MBED_CONF_STORAGE_FILESYSTEM_EXTERNAL_BASE_ADDRESS)
```		

## Change to smaller size of external SPI Flash ##

Above configuration reserves 1MiB for external KVStore. In addition to 415KiB (0x67C00) for candidate application firmware, you can reduce the external KVStore size for smaller SPI flash.

To use 1MiB external SPI flash, you can set storage_filesystem.external_size to (608*1024) (i.e. 608KiB) for external KVStore.

To use 512KiB external SPI flash, you can set storage_filesystem.external_size to (96*1024) (i.e. 96KiB) for external KVStore.

But you need to pay attention to round down the 608K or 96K to be multiple of erase block size of SPI flash.

# Build the example #

## Install Mbed CLI ##

Skip to next step if Mbed CLI is installed. Follows the [document](https://os.mbed.com/docs/mbed-os/latest/build-tools/install-and-set-up.html) to install Mbed CLI.


## Import and build Bootloader ##
```
>cd TO-YOUR-WORKING-DIRECTORY
>mbed import https://github.com/OpenNuvoton/mbed-bootloader#nuvoton_v4.1.3
>cd mbed-bootloader
```
Copy kvstore_and_fw_candidate_on_spif_nuvoton.json file to mbed-bootloader\configs directory. Toolchain can be GCC_ARM or ARMC6. Following command choose ARMC6.
```
>mbed compile –t ARMC6 –m NuMaker_IOT_M487 --profile=tiny.json --app-config=configs\kvstore_and_fw_candidate_on_spif_nuvoton.json
```

## Import and build Pelion example ##
```
>cd ..
>mbed import https://os.mbed.com/teams/Nuvoton/code/mbed-os-example-pelion
>cd mbed-os-example-pelion
```
Copy mbed-bootloader\BUILD\NUMAKER_IOT_M487\ARMC6-TINY\mbed-bootloader.bin to mbed-os-example-pelion\bootloader directory.

Copy mbed_app_spi.json file to mbed-os-example-pelion directory, then edit it to set Wi-Fi SSID and password.

```
    "target_overrides": {
        "*": {
    ...
            "nsapi.default-wifi-ssid"                   : "\"YOUR-WIFI-SSID\"",
            "nsapi.default-wifi-password"               : "\"YOUR-WIFI-PASSWORD\"",
    ...
```
Save the file then compile the code. Following command uses ARMC6 as toolchain.
```
>mbed compile –t ARMC6 –m NUMAKER_IOT_M487 –app-config mbed_app_spi.json
```
After build is complete, copy mbed-os-example-pelion\NUMAKER_IOT_M487\ARMC6\mbed-os-example-pelion.bin to your NuMicro drive to program the firmware to NuMaker-IoT-M487 board. Open the board’s VCOM to see the connection, registration, and a counter. You can see the counter on Pelion website dashboard, too.

Follow Pelion firmware update flow to build the new version firmware, sign and publish to Pelion to see the OTA functionality. 
