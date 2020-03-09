# Optiga Trust X module for Zephyr RTOS
Port of Optiga Trust X framework as an external module within Zephyr Project RTOS. More informations about this framework can be found on it's original repostiory [here](https://github.com/Infineon/optiga-trust-x).

# How to include framework into project
 - Change i2c and GPIO context for PAL in file [pal_ifx_i2c_config.h](pal/efm32pg-stk3402a/pal_ifx_i2c_config.c) (macros for Pins, Ports, etc.) basing on board you are using.
 - In *prj.conf* file set
 ```
 CONFIG_OPTIGA=y
 CONFIG_HEAP_MEM_POOL_SIZE=2048
 ```
 to enable Optiga Trust X support and to be able to use dynamic memory allocation functions.
 - Make sure that GPIO and I2C is set up properly in project configuration file.

 # Enabling mbedTLS
 - For enabling mbedTLS support include in *prj.conf* those options:
```
CONFIG_NEWLIB_LIBC=y
CONFIG_OPTIGA_MBEDTLS=y
CONFIG_MBEDTLS=y
CONFIG_MBEDTLS_HEAP_SIZE=56240
CONFIG_MBEDTLS_CFG_FILE="mbedtls_config.h"
```
 - Set Public and Private Key slots in Kconfig (by defaults slot are in position 0).

# Changes made in API to work with Zephyr
 - Dynamic memory management changed in file [MemoryMgmt.h](optiga/include/optiga/common/MemoryMgmt.h) (swaped for Zephyr's methods)
 - Macro **MBEDTLS_ENTROPY_MIN_HARDWARE** is set 0 in *mbedtls_config.h*. Based on point **Limited sources** in [mbedTLS documentation](https://tls.mbed.org/kb/how-to/add-entropy-sources-to-entropy-pool). When set on default *32*, demo *example_authenticate_chip.c* is getting stuck.
