# No XRAM configuration for targets with XRAM support

This is a simple guide to remove external SRAM (XRAM) support for a target which has it.
Some customers may wish their final products not attached with XRAM for cost consideration.
The no-XRAM configuration is provided for their evaluation.

Currently, only the *NuMaker-PFM-NUC472* board (*NUMAKER_PFM_NUC472* target) has XRAM support, and it is taken as an example for explanation.

## No XRAM configuration
By default, XRAM support is enabled in the *NUMAKER_PFM_NUC472* target.
To remove it, create a mbed configuration file `mbed_app.json` (see the [mbed configuration system](https://docs.mbed.com/docs/mbed-os-handbook/en/5.3/advanced/config_system/))
in the root path of your application with the following content:

```
{
    "target_overrides": {
        "NUMAKER_PFM_NUC472": {
            "target.extra_labels_remove": ["NU_XRAM_SUPPORTED"],
            "target.extra_labels_add": ["NU_XRAM_UNSUPPORTED"]
        }
    }
}
```

The *NUMAKER_PFM_NUC472* target supports `LWIP`. You could meet OOM issue on compiling `LWIP` examples because XRAM support has been removed.
To avoid confusion, you could also remove the `LWIP` support:

```
{
    "target_overrides": {
        "NUMAKER_PFM_NUC472": {
            "target.extra_labels_remove": ["NU_XRAM_SUPPORTED"],
            "target.extra_labels_add": ["NU_XRAM_UNSUPPORTED"],
            "target.features_remove": ["LWIP"]
        }
    }
}
```
