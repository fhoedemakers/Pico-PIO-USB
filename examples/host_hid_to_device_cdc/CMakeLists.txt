set(target_name host_hid_to_device_cdc)
add_executable(${target_name})

target_sources(${target_name} PRIVATE
 host_hid_to_device_cdc.c
 usb_descriptors.c
 # can use 'tinyusb_pico_pio_usb' library later when pico-sdk is updated
 ${PICO_TINYUSB_PATH}/src/portable/raspberrypi/pio_usb/dcd_pio_usb.c
 ${PICO_TINYUSB_PATH}/src/portable/raspberrypi/pio_usb/hcd_pio_usb.c
 )

 # print memory usage, enable all warnings
target_link_options(${target_name} PRIVATE -Xlinker --print-memory-usage)
target_compile_options(${target_name} PRIVATE -Wall -Wextra)

# use tinyusb implementation
# Adafruit Metro RP2350 using built-in USB with D+ GPIO32, D- GPIO33 : not working
target_compile_definitions(${target_name} PRIVATE PIO_USB_USE_TINYUSB PIO_USB_DP_PIN_DEFAULT=32 PICO_PIO_USE_GPIO_BASE=1 PICO_DEFAULT_PIO_USB_DP_PIN=29 PICO_DEFAULT_PIO_USB_VBUSEN_STATE=1)
#  Adafruit Metro RP2350 with External USB port on D+ GPIO 4, D- GPIO5 : working
#target_compile_definitions(${target_name} PRIVATE PIO_USB_USE_TINYUSB PIO_USB_DP_PIN_DEFAULT=4)
# needed so tinyusb can find tusb_config.h
target_include_directories(${target_name} PRIVATE ${CMAKE_CURRENT_LIST_DIR})

target_link_libraries(${target_name} PRIVATE pico_stdlib pico_pio_usb tinyusb_device tinyusb_host)
pico_add_extra_outputs(${target_name})

