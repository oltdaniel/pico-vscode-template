cmake_minimum_required(VERSION 3.13)

# Import pico-sdk related functions
include(pico-sdk/pico_sdk_init.cmake)
# Import toolchain configuration
include(toolchains/gcc-arm-none-eabi_toolchain.cmake)

# Our porject supports C,C++ and Assembly
project(pico C CXX ASM)
# We follow the C11 Standard
set(CMAKE_C_STANDARD 11)

# We want debug information in our binaries
set(default_build_type "Debug")
# We don't need UF2 files
set(PICO_NO_UF2 on)

# Initialize pico-sdk related functions
pico_sdk_init()

# Find all project relevant files
file(GLOB SRC_FOLDER CONFIGURE_DEPENDS "src/*.h" "src/*.c" "src/*.s")
# Create our main executable
add_executable(main
    ${SRC_FOLDER})
# Add src to include directories for tinyusb support
target_include_directories(main PUBLIC
    "src")

# Add pico_stdlib library which aggregates commonly used features
target_link_libraries(main PUBLIC pico_stdlib)

# Add stdio USB serial output
pico_enable_stdio_usb(main 1)
# Disable UART stdio output
pico_enable_stdio_uart(main 0)
