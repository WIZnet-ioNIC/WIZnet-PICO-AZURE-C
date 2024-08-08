<!-- omit in toc -->
# Raspberry Pi Pico W5x00 Azure IoT SDK Examples

RP2040 - W5100S or W5500 network examples - Azure IoT Cloud functions, Azure IoT SDK, Azure IoT device client, ...

- [1. 🎯 Azure IoT SDK examples](#1--azure-iot-sdk-examples)
  - [1.1. 3rd party SDKs & libraries](#11-3rd-party-sdks--libraries)
- [2. 🎓 Getting started](#2--getting-started)
  - [2.1. 🗂 Set up example](#21--set-up-example)
    - [2.1.1. Make 'port' directory for azure-iot-sdk-c, ioLibrary_Driver, mbedtls and timer](#211-make-port-directory-for-azure-iot-sdk-c-iolibrary_driver-mbedtls-and-timer)
    - [2.1.2. Modify 'CMakeLists.txt'](#212-modify-cmakeliststxt)
    - [2.1.3. Set your board network information and select sample application](#213-set-your-board-network-information-and-select-sample-application)
    - [2.1.4. Set the key information](#214-set-the-key-information)
  - [2.2. ⏳ Build example](#22--build-example)
    - [2.2.1. Run command](#221-run-command)
    - [2.2.2. Example command log](#222-example-command-log)
  - [2.3. 📝 Sample application results](#23--sample-application-results)
    - [2.3.1. 📬 'iothub_ll_telemetry_sample' application result](#231--iothub_ll_telemetry_sample-application-result)
    - [2.3.2. 📩 'iothub_ll_c2d_sample' application result](#232--iothub_ll_c2d_sample-application-result)
    - [2.3.3. 🔐 'iothub_ll_client_x509_sample' application result](#233--iothub_ll_client_x509_sample-application-result)
    - [2.3.4. 🚢 'prov_dev_client_ll_sample' application result](#234--prov_dev_client_ll_sample-application-result)

------



# 1. 🎯 Azure IoT SDK examples

| Application         | Description                                                                          |
| :-----------------: | ------------------------------------------------------------------------------------ |
|[examples](examples) | Basic Azure IoT Cloud functions with Azure IoT SDK. (NonOS + WIZnet W5100S, W5500 or W55RP20) |



## 1.1. 3rd party SDKs & libraries

3rd party SDKs & libraries are in the `WIZnet-PICO-AZURE-C\libraries` directory of 'WIZnet-PICO-AZURE-C', the example for connecting Azure IoT Cloud.

| SDKs & libraries                                               | Description |
| :------------------------------------------------------------: | ----------- |
| [ioLibrary_Driver](https://github.com/Wiznet/ioLibrary_Driver) | A library that can control WIZnet's W5x00 series Ethernet chip. |
| [mbedtls](https://github.com/ARMmbed/mbedtls)                  | It supports security algorithms and SSL and TLS connection. |
| [azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c)    | A collection of C source files consisting of Embedded C (C-SDK) that can be used by embedded applications to securely connect IoT devices to Azure IoT Cloud. |
| [pico-sdk](https://github.com/raspberrypi/pico-sdk)            | It makes a development environment for building software applications for the Pico platform. |
| [pico-extras](https://github.com/raspberrypi/pico-extras)      | pico-extras has additional libraries that are not yet ready for inclusion the Pico SDK proper, or are just useful but don't necessarily belong in the Pico SDK. |

Each SDKs & libraries consists of submodule.



# 2. 🎓 Getting started

Please refer to [Getting Started with the Raspberry Pi Pico](https://rptl.io/pico-get-started) and the README in the [pico-sdk](https://github.com/raspberrypi/pico-sdk) for information on getting up and running.



## 2.1. 🗂 Set up example



### 2.1.1. Make 'port' directory for azure-iot-sdk-c, ioLibrary_Driver, mbedtls and timer

For Pico - W5100S, W5500 or W55RP20 platform, we need to port code, please check porting guide below.

- [How to Port the Azure IoT C SDK to Other Platforms](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

Result of above porting can be found in `WIZnet-PICO-AZURE-C\port\azure-iot-sdk-c` directory.



### 2.1.2. Modify 'CMakeLists.txt'



First, set the ethernet chip according to the evaluation board used in the following [`WIZnet-PICO-AZURE-C/CMakeLists.txt`](CMakeLists.txt) file.

- WIZnet Ethernet HAT
- W5100S-EVB-Pico
- W5500-EVB-Pico
- W55RP20-EVB-Pico

For example, when using WIZnet Ethernet HAT :

```bash
# Set board
set(BOARD_NAME WIZnet_Ethernet_HAT)
```

When using W5500-EVB-Pico:

```bash
# Set board
set(BOARD_NAME W5500_EVB_PICO)
```

And find the line similar to this and replace it as your environment:

```bash
# Set the project root directory if it's not already defined, as may happen if
# the tests folder is included directly by a parent project, without including
# the top level CMakeLists.txt.
if(NOT DEFINED AZURE_SDK_DIR)
    set(AZURE_SDK_DIR ${CMAKE_SOURCE_DIR}/libraries/azure-iot-sdk-c)
    message(STATUS "AZURE_SDK_DIR = ${AZURE_SDK_DIR}")
endif()

if(NOT DEFINED WIZNET_DIR)
    set(WIZNET_DIR ${CMAKE_SOURCE_DIR}/libraries/ioLibrary_Driver)
    message(STATUS "WIZNET_DIR = ${WIZNET_DIR}")
endif()

if(NOT DEFINED MBEDTLS_LIB_DIR)
    set(MBEDTLS_LIB_DIR ${CMAKE_SOURCE_DIR}/libraries/mbedtls)
    message(STATUS "MBEDTLS_LIB_DIR = ${MBEDTLS_LIB_DIR}")
endif()

if(NOT DEFINED PORT_DIR)
    set(PORT_DIR ${CMAKE_SOURCE_DIR}/port)
    message(STATUS "PORT_DIR = ${PORT_DIR}")
endif()
```



### 2.1.3. Set your board network information and select sample application

In the following [`WIZnet-PICO-AZURE-C/examples/main.c`](examples/main.c) source file, find the line similar to this and replace it as you want:

```C
(...)

// The application you wish to use should be uncommented
//
#define APP_TELEMETRY
//#define APP_C2D
//#define APP_CLI_X509
//#define APP_PROV_X509

// The application you wish to use DHCP mode should be uncommented
#define _DHCP
static wiz_NetInfo g_net_info =
    {
        .mac = {0x00, 0x08, 0xDC, 0x12, 0x34, 0x56}, // MAC address
        .ip = {192, 168, 11, 2},                     // IP address
        .sn = {255, 255, 255, 0},                    // Subnet Mask
        .gw = {192, 168, 11, 1},                     // Gateway
        .dns = {8, 8, 8, 8},                         // DNS server
#ifdef _DHCP
        .dhcp = NETINFO_DHCP // DHCP enable/disable
#else
        // this example uses static IP
        .dhcp = NETINFO_STATIC
#endif
};
```



### 2.1.4. Set the key information

Copy & Paste proper connection string and key value from the Azure Portal to [`WIZnet-PICO-AZURE-C/examples/sample_certs.c`](examples/sample_certs.c):

```C
/* Paste in the your iothub connection string  */
const char pico_az_connectionString[] = "[device connection string]";

const char pico_az_x509connectionString[] = "[device connection string]";

const char pico_az_x509certificate[] =
"-----BEGIN CERTIFICATE-----""\n"
"-----END CERTIFICATE-----";

const char pico_az_x509privatekey[] =
"-----BEGIN PRIVATE KEY-----""\n"
"-----END PRIVATE KEY-----";

const char pico_az_id_scope[] = "[ID Scope]";

const char pico_az_COMMON_NAME[] = "[custom-hsm-device]";

const char pico_az_CERTIFICATE[] =
"-----BEGIN CERTIFICATE-----""\n"
"-----END CERTIFICATE-----";

const char pico_az_PRIVATE_KEY[] =
"-----BEGIN PRIVATE KEY-----""\n"
"-----END PRIVATE KEY-----";
```



## 2.2. ⏳ Build example



### 2.2.1. Run command

Run the following CMake commands from the root of the repository:

```
mkdir build
cd build

# As your environment, select
cmake .. -G "MSYS Makefiles" ## on MSYS2 (MinGW64) + Windows 10 Platform
# or
cmake .. -G "Visual Studio 15 2017" ## For Visual Studio 2017
# or
cmake .. -G "Visual Studio 16 2019" -A Win32
# or
cmake ..

cd examples
make
```

Then, copy generated "main.uf2" file into your RP-Pico board. Done!




## 2.3. 📝 Sample application results



### 2.3.1. 📬 'iothub_ll_telemetry_sample' application result

📑 [Let's see this doc for iothub_ll_telemetry_sample application](_1_APP_TELEMETRY_manual.md)



### 2.3.2. 📩 'iothub_ll_c2d_sample' application result

📑 [Let's see this doc for iothub_ll_c2d_sample application](_2_APP_C2D_manual.md)



### 2.3.3. 🔐 'iothub_ll_client_x509_sample' application result

📑 [Let's see this doc for iothub_ll_client_x509_sample application](_3_APP_CLIENT_X509_manual.md)



### 2.3.4. 🚢 'prov_dev_client_ll_sample' application result

📑 [Let's see this doc for prov_dev_client_ll_sample application](_4_APP_PROV_X509_manual.md)
