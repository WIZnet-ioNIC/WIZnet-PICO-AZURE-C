# ioLibrary_Driver

add_library(IOLIBRARY_FILES STATIC)

target_sources(IOLIBRARY_FILES PUBLIC
        ${PORT_DIR}/ioLibrary_Driver/src/wizchip_spi.c
        ${PORT_DIR}/ioLibrary_Driver/src/wizchip_gpio_irq.c
        )

if(${BOARD_NAME} STREQUAL W55RP20_EVB_PICO OR ${WIZNET_CHIP} STREQUAL W6300)
        pico_generate_pio_header(IOLIBRARY_FILES ${PORT_DIR}/ioLibrary_Driver/src/wizchip_qspi_pio.pio)

target_sources(IOLIBRARY_FILES PUBLIC 
        ${PORT_DIR}/ioLibrary_Driver/src/wizchip_qspi_pio.c
)

target_include_directories(IOLIBRARY_FILES PUBLIC
        ${WIZNET_DIR}/Ethernet
        ${PORT_DIR}/ioLibrary_Driver/inc
        ${PORT_DIR}
)
endif()
        
target_include_directories(IOLIBRARY_FILES PUBLIC
        ${WIZNET_DIR}/Ethernet
        ${PORT_DIR}/ioLibrary_Driver/inc
        ${PORT_DIR}
        )

target_link_libraries(IOLIBRARY_FILES PUBLIC
        pico_stdlib
        hardware_pio
        hardware_spi
        hardware_dma
        hardware_clocks
        )

# timer
add_library(TIMER_FILES STATIC)

target_sources(TIMER_FILES PUBLIC
        ${PORT_DIR}/timer/timer.c
        )

target_include_directories(TIMER_FILES PUBLIC
        ${PORT_DIR}/timer
        )

target_link_libraries(TIMER_FILES PRIVATE
        pico_stdlib      
        )



# port_common
add_library(PORT_COMMON_FILES STATIC)

target_include_directories(PORT_COMMON_FILES PUBLIC
${PORT_DIR}
)

target_link_libraries(PORT_COMMON_FILES PUBLIC
pico_stdlib
hardware_clocks
hardware_spi
hardware_dma
hardware_pio
)

# azure-iot-sdk-c
add_library(AZURE_SDK_PORT_FILES STATIC)

target_sources(AZURE_SDK_PORT_FILES PUBLIC
        ${PORT_DIR}/azure-iot-sdk-c/src/agenttime.c
        ${PORT_DIR}/azure-iot-sdk-c/src/platform.c
        ${PORT_DIR}/azure-iot-sdk-c/src/netif.c
        ${PORT_DIR}/azure-iot-sdk-c/src/netutil.c
        ${PORT_DIR}/azure-iot-sdk-c/src/tcpsocketconnection_c.c
        ${PORT_DIR}/azure-iot-sdk-c/src/tlsio_mbedtls.c
        ${PORT_DIR}/azure-iot-sdk-c/src/lock.c
        ${PORT_DIR}/azure-iot-sdk-c/src/threadapi_nonos.c
        ${PORT_DIR}/azure-iot-sdk-c/src/tickcounter_nonos.c
        )

target_include_directories(AZURE_SDK_PORT_FILES PUBLIC
        ${PORT_DIR}/azure-iot-sdk-c/inc
        ${MBEDTLS_DIR}/include
        )

target_link_libraries(AZURE_SDK_PORT_FILES PUBLIC
        pico_stdlib
        ETHERNET_FILES
        DHCP_FILES
        DNS_FILES
        SNTP_FILES
        AZURE_SDK_FILES
        )
