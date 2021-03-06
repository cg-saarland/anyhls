project(AnyHLS)
cmake_minimum_required(VERSION 3.4.3)

find_package(AnyDSL_runtime REQUIRED)
set(CMAKE_CXX_STANDARD 14)

enable_testing()

include_directories(${AnyDSL_runtime_INCLUDE_DIRS})

set(BACKEND fpga)
if(NOT BACKEND)
    set(BACKEND aocl CACHE STRING "select the backend from the following: CPU, AVX, NVVM, CUDA, OPENCL, AMDGPU, fpga" FORCE)
endif()
string(TOLOWER "${BACKEND}" BACKEND)
message(STATUS "Selected backend: ${BACKEND}")

if(BACKEND STREQUAL "fpga")
    set(SYNTHESIS OFF CACHE BOOL "ON: Synthesis and HDL generation, OFF: C simulation")
    message(STATUS "HW Synthesis: ${SYNTHESIS}")

    set(FPGA_PART xc7z020clg484-1 CACHE STRING "Specify FPGA part")
    string(TOLOWER "${FPGA_PART}" FPGA_PART)
    message(STATUS "Selected FPGA: ${FPGA_PART}")

    set(SOC OFF CACHE BOOL "ON: SoC integration, OFF: HDL IP generation")
    message(STATUS "SoC integration: ${SOC}")
else()
    unset(SYNTHESIS CACHE)
    unset(FPGA_PART CACHE)
    unset(SOC CACHE)
endif()

set(ANYHLS_CORE_FILES
    ${PROJECT_SOURCE_DIR}/core/src/utils.impala
    ${PROJECT_SOURCE_DIR}/core/src/backend.impala
    ${PROJECT_SOURCE_DIR}/core/src/fsm.impala
    ${PROJECT_SOURCE_DIR}/core/src/red.impala
    ${PROJECT_SOURCE_DIR}/core/src/mux.impala
    ${PROJECT_SOURCE_DIR}/core/src/regs1d.impala
    ${PROJECT_SOURCE_DIR}/core/src/regs2d.impala)

set(ANYHLS_CORE_ANY_FILE ${ANYHLS_CORE_FILES} ${PROJECT_SOURCE_DIR}/core/src/datat_any.impala)
set(ANYHLS_CORE_I32_FILE ${ANYHLS_CORE_FILES} ${PROJECT_SOURCE_DIR}/core/src/datat_i32.impala)
set(ANYHLS_CORE_F32_FILE ${ANYHLS_CORE_FILES} ${PROJECT_SOURCE_DIR}/core/src/datat_f32.impala)
set(ANYHLS_CORE_U8_FILE  ${ANYHLS_CORE_FILES} ${PROJECT_SOURCE_DIR}/core/src/datat_u8.impala)

add_subdirectory(core)
add_subdirectory(imglib)
