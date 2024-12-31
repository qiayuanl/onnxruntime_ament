cmake_minimum_required(VERSION 3.8)
project(onnxruntime_ament)

find_package(ament_cmake REQUIRED)

include(FetchContent)

# Define directories
set(ONNXRUNTIME_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX} CACHE STRING "ONNXRUNTIME install path")
set(ONNXRUNTIME_INCLUDE_DIR ${ONNXRUNTIME_INSTALL_PREFIX}/include)
set(ONNXRUNTIME_LIB_DIR ${ONNXRUNTIME_INSTALL_PREFIX}/lib)
set(ONNXRUNTIME_DOWNLOAD_DIR ${CMAKE_CURRENT_BINARY_DIR}/download)
set(ONNXRUNTIME_INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}/onnxruntime)

# Create directories if they do not exist
file(MAKE_DIRECTORY ${ONNXRUNTIME_DOWNLOAD_DIR})
file(MAKE_DIRECTORY ${ONNXRUNTIME_INSTALL_DIR})

# Download ONNX Runtime
FetchContent_Declare(
        onnxruntimeDownload
        URL https://github.com/microsoft/onnxruntime/releases/download/v1.11.0/onnxruntime-linux-x64-1.11.0.tgz
        UPDATE_COMMAND ""
        SOURCE_DIR ${ONNXRUNTIME_DOWNLOAD_DIR}
)
FetchContent_MakeAvailable(onnxruntimeDownload)

# Create wrapper library (INTERFACE only, no output file)
add_library(${PROJECT_NAME} INTERFACE)

# Link include directories and libraries
target_include_directories(${PROJECT_NAME} INTERFACE ${ONNXRUNTIME_INCLUDE_DIR})
target_link_directories(${PROJECT_NAME} INTERFACE ${ONNXRUNTIME_LIB_DIR})
target_link_libraries(${PROJECT_NAME} INTERFACE onnxruntime)

# Install ONNX Runtime headers and libraries
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

# Export only the include directories and libraries, not the INTERFACE library itself
ament_export_include_directories(${ONNXRUNTIME_INCLUDE_DIR})
ament_export_libraries(onnxruntime)
ament_package()
