cmake_minimum_required(VERSION 3.1.0 FATAL_ERROR)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/build/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/build/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/build/bin)
include(cmake/FrameworkUtils.cmake)
#set(CMAKE_VERBOSE_MAKEFILE ON)

project(litiv_framework)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules/")
set(LITIV_FRAMEWORK_VERSION_MAJOR 1)
set(LITIV_FRAMEWORK_VERSION_MINOR 0)
set(LITIV_FRAMEWORK_VERSION_PATCH 0)
set(LITIV_FRAMEWORK_VERSION ${LITIV_FRAMEWORK_VERSION_MAJOR}.${LITIV_FRAMEWORK_VERSION_MINOR}.${LITIV_FRAMEWORK_VERSION_PATCH})
set(CMAKE_CXX_FLAGS "-march=native -Wall -Wextra -Wfatal-errors")

option(USE_TEST "Use some useless global test define" ON)

### OPENCV CHECK
find_package(OpenCV 3.0 REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})
link_directories(${OpenCV_LIBRARY_DIRS})
add_definitions(${OpenCV_DEFINITIONS})

### GLSL CHECK
find_package(GLFW REQUIRED)
find_package(OpenGL REQUIRED)
find_package(GLEW REQUIRED)
find_package(GLM REQUIRED)
set_eval(USE_GLSL (${GLFW_FOUND} AND ${OPENGL_FOUND} AND ${GLEW_FOUND} AND ${GLM_FOUND}))
if(USE_GLSL)
    include_directories(${GLFW_INCLUDE_DIRS})
    include_directories(${OpenGL_INCLUDE_DIRS})
    include_directories(${GLEW_INCLUDE_DIRS})
    include_directories(${GLM_INCLUDE_DIRS})
    set(GLSL_LIBRARIES ${GLFW_LIBRARIES}${OPENGL_LIBRARIES}${GLEW_LIBRARIES}${GLM_LIBRARIES})
endif(USE_GLSL)
message(STATUS USE_GLSL=${USE_GLSL})

### CUDA CHECK @@@@
set_eval(USE_CUDA 0)

### OPENCL CHECK @@@@
set_eval(USE_OPENCL 0)

### HARDWARE SIMD CHECK @@@@
if(CMAKE_CROSSCOMPILING)
    option(USE_MMX "Use MMX instructions support" OFF)
    option(USE_SSE "Use SSE instructions support" OFF)
    option(USE_SSE2 "Use SSE2 instructions support" OFF)
    option(USE_SSE3 "Use SSE3 instructions support" OFF)
    option(USE_SSSE3 "Use SSSE3 instructions support" OFF)
    option(USE_SSE4_1 "Use SSE4_1 instructions support" OFF)
    option(USE_SSE4_2 "Use SSE4_2 instructions support" OFF)
    option(USE_POPCNT "Use POPCNT instructions support" OFF)
    option(USE_AVX "Use AVX instructions support" OFF)
    option(USE_AVX2 "Use AVX2 instructions support" OFF)
else(CMAKE_CROSSCOMPILING)
    try_cvhardwaresupport_runcheck_and_set_success(MMX)
    try_cvhardwaresupport_runcheck_and_set_success(SSE)
    try_cvhardwaresupport_runcheck_and_set_success(SSE2)
    try_cvhardwaresupport_runcheck_and_set_success(SSE3)
    try_cvhardwaresupport_runcheck_and_set_success(SSSE3)
    try_cvhardwaresupport_runcheck_and_set_success(SSE4_1)
    try_cvhardwaresupport_runcheck_and_set_success(SSE4_2)
    try_cvhardwaresupport_runcheck_and_set_success(POPCNT)
    try_cvhardwaresupport_runcheck_and_set_success(AVX)
    try_cvhardwaresupport_runcheck_and_set_success(AVX2)
endif(CMAKE_CROSSCOMPILING)

add_subdirectory(modules)
add_subdirectory(execs)