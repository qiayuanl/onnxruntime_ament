cmake_minimum_required(VERSION 3.8)
project(onnxruntime_ament)

find_package(ament_cmake REQUIRED)

# Define directories
set(ONNXRUNTIME_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX} CACHE STRING "ONNXRUNTIME install path")
set(ONNXRUNTIME_INCLUDE_DIR ${ONNXRUNTIME_INSTALL_PREFIX}/include)
set(ONNXRUNTIME_LIB_DIR ${ONNXRUNTIME_INSTALL_PREFIX}/lib)
set(ONNXRUNTIME_DOWNLOAD_DIR ${CMAKE_CURRENT_BINARY_DIR}/download)
set(ONNXRUNTIME_INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}/onnxruntime)

# Create directories if they do not exist
file(MAKE_DIRECTORY ${ONNXRUNTIME_DOWNLOAD_DIR})
file(MAKE_DIRECTORY ${ONNXRUNTIME_INSTALL_DIR})

# Define the base URL and version for the library
set(ONNXRUNTIME_VERSION 1.11.0)
set(ONNXRUNTIME_BASE_URL https://github.com/microsoft/onnxruntime/releases/download/v${ONNXRUNTIME_VERSION})
if (CMAKE_SYSTEM_PROCESSOR STREQUAL "aarch64")
    set(ONNXRUNTIME_PLATFORM linux-aarch64)
else ()
    set(ONNXRUNTIME_PLATFORM linux-x64)
endif ()
set(ONNXRUNTIME_URL ${ONNXRUNTIME_BASE_URL}/onnxruntime-${ONNXRUNTIME_PLATFORM}-${ONNXRUNTIME_VERSION}.tgz)

include(FetchContent)
FetchContent_Declare(
        onnxruntimeDownload
        URL ${ONNXRUNTIME_URL}
        UPDATE_COMMAND ""
        SOURCE_DIR ${ONNXRUNTIME_DOWNLOAD_DIR}
)
FetchContent_MakeAvailable(onnxruntimeDownload)

# Create wrapper library (INTERFACE only, no output file)
add_library(${PROJECT_NAME} INTERFACE)

target_include_directories(${PROJECT_NAME} INTERFACE ${ONNXRUNTIME_INCLUDE_DIR})
target_link_directories(${PROJECT_NAME} INTERFACE ${ONNXRUNTIME_LIB_DIR})
target_link_libraries(${PROJECT_NAME} INTERFACE onnxruntime)

install(
        DIRECTORY ${ONNXRUNTIME_DOWNLOAD_DIR}/include/
        DESTINATION include/onnxruntime
)

install(
        FILES
        ${ONNXRUNTIME_DOWNLOAD_DIR}/lib/libonnxruntime.so
        ${ONNXRUNTIME_DOWNLOAD_DIR}/lib/libonnxruntime.so.1.11.0
        DESTINATION lib
)

ament_export_include_directories(${ONNXRUNTIME_INCLUDE_DIR})
ament_export_libraries(onnxruntime)
ament_package()
