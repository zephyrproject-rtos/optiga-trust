# CMakeLists.txt for Optiga Trust X security chip module
if (CONFIG_OPTIGA)
    add_library(optiga-trust-x INTERFACE)

    # Include paths
    zephyr_include_directories(
    	optiga/include
    )
    if (CONFIG_OPTIGA_MBEDTLS)
        # Include path for mbedtls library and examples
        zephyr_include_directories(
            examples/ecdsa_utils
            examples/authenticate_chip
        )
        target_compile_definitions(optiga-trust-x INTERFACE
            MBEDTLS_CONFIG_FILE="${CONFIG_MBEDTLS_CFG_FILE}"
        )
    endif ()

    zephyr_library()        

    # Sources paths
    zephyr_library_sources(
        optiga/cmd/CommandLib.c
        optiga/common/Logger.c
        optiga/common/Util.c
        optiga/crypt/optiga_crypt.c
        optiga/util/optiga_util.c
    )
    zephyr_library_sources(
        optiga/comms/optiga_comms.c
        optiga/comms/ifx_i2c/ifx_i2c_config.c
        optiga/comms/ifx_i2c/ifx_i2c_data_link_layer.c
        optiga/comms/ifx_i2c/ifx_i2c_physical_layer.c
        optiga/comms/ifx_i2c/ifx_i2c_transport_layer.c
        optiga/comms/ifx_i2c/ifx_i2c.c
    )
    zephyr_library_sources(
        pal/zephyr/pal_gpio.c
        pal/zephyr/pal_i2c.c
        pal/zephyr/pal_ifx_i2c_config.c
        pal/zephyr/pal_os_event.c
        pal/zephyr/pal_os_lock.c
        pal/zephyr/pal_os_timer.c
    )

    # Examples
    zephyr_library_sources(
        examples/optiga/example_optiga_crypt_ecc_generate_keypair.c
        examples/optiga/example_optiga_crypt_ecdh.c
        examples/optiga/example_optiga_crypt_ecdsa_sign.c
        examples/optiga/example_optiga_crypt_ecdsa_verify.c
        examples/optiga/example_optiga_crypt_hash.c
        examples/optiga/example_optiga_crypt_random.c
        examples/optiga/example_optiga_crypt_tls_prf_sha256.c
        examples/optiga/example_optiga_util_read_data.c
        examples/optiga/example_optiga_util_write_data.c
    )

    if (CONFIG_OPTIGA_MBEDTLS)
        # Sources
        zephyr_library_sources(
            examples/authenticate_chip/pal_crypt_mbedtls.c
            examples/mbedtls_port/trustx_ecdh.c
            examples/mbedtls_port/trustx_ecdsa.c
            examples/mbedtls_port/trustx_random.c
            examples/ecdsa_utils/ecdsa_utils.c
        )
        # Examples
        zephyr_library_sources(
            examples/authenticate_chip/example_authenticate_chip.c
        )
    endif () # CONFIG_OPTIGA_MBEDTLS

    # Link diffrently depending on config
    if(CONFIG_OPTIGA_MBEDTLS)
        zephyr_library_link_libraries(optiga-trust-x)
        target_link_libraries(optiga-trust-x INTERFACE mbedTLS)
    else ()
        zephyr_library_link_libraries(optiga-trust-x)
    endif () # CONFIG_OPTIGA_MBEDTLS
endif () # CONFIG_OPTIGA
