cmake_minimum_required(VERSION 3.22)

# --- ARCH LINUX FIX: COMPILER SANITY & ARCHITECTURE ---
set(CMAKE_TRY_COMPILE_TARGET_TYPE "STATIC_LIBRARY")
set(CMAKE_EXECUTABLE_SUFFIX_C ".elf")

# Force Cortex-M4 Architecture Flags
add_compile_options(
    -mcpu=cortex-m4 
    -mthumb 
    -mfpu=fpv4-sp-d16 
    -mfloat-abi=hard
)
# ---------------------------------------------------------

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS ON)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Debug")
endif()

set(CMAKE_PROJECT_NAME blinky_sandbox)
set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE)

project(${CMAKE_PROJECT_NAME})

enable_language(C ASM)

add_executable(${CMAKE_PROJECT_NAME})

# Include the CubeMX drivers
add_subdirectory(cmake/stm32cubemx)

target_link_libraries(${CMAKE_PROJECT_NAME}
    stm32cubemx
)

# Apply Architecture Flags and Linker Script to the final binary
target_link_options(${CMAKE_PROJECT_NAME} PRIVATE
    -mcpu=cortex-m4
    -mthumb
    -mfpu=fpv4-sp-d16
    -mfloat-abi=hard
    "-T${CMAKE_CURRENT_SOURCE_DIR}/STM32F401RETX_FLASH.ld"
    "--specs=nosys.specs"
    "-Wl,-Map=${CMAKE_PROJECT_NAME}.map,--cref"
    "-Wl,--gc-sections"
)