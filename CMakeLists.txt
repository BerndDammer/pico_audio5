# freertos/lwip template // cmake template  taken from
# pico-examples/pico_w/wifi/freertos/iperf

# Generated Cmake Pico project file

cmake_minimum_required(VERSION 3.13)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# Initialise pico_sdk from installed location
# (note this can come from environment, CMake cache etc)
# set(PICO_SDK_PATH "d:/RaspberryPiPico2/pico-sdk")

set(PICO_BOARD pico CACHE STRING "Board type")

# Pull in Raspberry Pi Pico SDK (must be before project)
include(pico_sdk_import.cmake)

if (PICO_SDK_VERSION_STRING VERSION_LESS "1.4.0")
  message(FATAL_ERROR "Raspberry Pi Pico SDK version 1.4.0 (or later) required. Your version is ${PICO_SDK_VERSION_STRING}")
endif()


# ///////////////////////////////////////////////////// freertos

### # // WORKAROUND: Why must be set now and not before
### set(FREE_RTOS_KERNEL_SMP 1)


set(FREERTOS_KERNEL_PATH ${CMAKE_CURRENT_LIST_DIR}/freertos)
include(FreeRTOS_Kernel_import.cmake)

# ///////////////////////////////


project(pico_audio5 C CXX ASM)

# Initialise the Raspberry Pi Pico SDK
pico_sdk_init()



#add_subdirectory(src)
#include_directories(${MyProject_SOURCE_DIR}/src)

add_executable(pico_audio5
	src/entry.c 
	src/panic.c
	src/task_blinker.c
	src/task_console.c
	src/task_tud.c
	src/usb_descriptors.c
	src/board_usb_get_serial.c
	src/usb_device_midi.c
)


target_include_directories(pico_audio5 PRIVATE
	freertos/include
	freertos/portable/ThirdParty/GCC/RP2040/include/
)

# don't want NO_SYS (generally this would be in your lwipopts.h)
target_compile_definitions(pico_audio5 PRIVATE NO_SYS=0 )

pico_set_program_name(pico_audio5 "pico_audio5")
pico_set_program_version(pico_audio5 "0.1.0")

pico_enable_stdio_uart(pico_audio5 1)
pico_enable_stdio_usb(pico_audio5 0)

# Add the standard include files to the build
target_include_directories(pico_audio5 PRIVATE
  ${CMAKE_CURRENT_LIST_DIR}
  ${CMAKE_CURRENT_LIST_DIR}/../.. # for our common lwipopts or any other standard includes, if required
)

# Add any user requested libraries
target_link_libraries(pico_audio5
    pico_stdlib
#    hardware_spi
    hardware_timer
    hardware_clocks
#    hardware_pwm
    FreeRTOS-Kernel-Heap4
    tinyusb_device
)

pico_add_extra_outputs(pico_audio5)

