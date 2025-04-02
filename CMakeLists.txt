cmake_minimum_required(VERSION 3.8)
project(onnxruntime_ament)

find_package(ament_cmake REQUIRED)

# Define directories, and create directories if they do not exist
set(ONNXRUNTIME_DOWNLOAD_DIR ${CMAKE_CURRENT_BINARY_DIR}/download)
file(MAKE_DIRECTORY ${ONNXRUNTIME_DOWNLOAD_DIR})

# Define the base URL and version for the library
set(ONNXRUNTIME_VERSION 1.12.1)
set(ONNXRUNTIME_BASE_URL https://github.com/microsoft/onnxruntime/releases/download/v${ONNXRUNTIME_VERSION})
if (CMAKE_SYSTEM_PROCESSOR STREQUAL "aarch64")
    set(ONNXRUNTIME_PLATFORM linux-aarch64)
else ()
    set(ONNXRUNTIME_PLATFORM linux-x64)
endif ()

include(FetchContent)
FetchContent_Declare(
    onnxruntimeDownload
    URL https://github.com/microsoft/onnxruntime/releases/download/v${ONNXRUNTIME_VERSION}/onnxruntime-${ONNXRUNTIME_PLATFORM}-${ONNXRUNTIME_VERSION}.tgz
    UPDATE_COMMAND ""
    SOURCE_DIR ${ONNXRUNTIME_DOWNLOAD_DIR}
)
FetchContent_MakeAvailable(onnxruntimeDownload)

# Create wrapper library (INTERFACE only, no output file)
add_library(${PROJECT_NAME} INTERFACE)

install(
    DIRECTORY ${ONNXRUNTIME_DOWNLOAD_DIR}/include/
    DESTINATION include/onnxruntime
)
install(
    FILES
    ${ONNXRUNTIME_DOWNLOAD_DIR}/lib/libonnxruntime.so
    ${ONNXRUNTIME_DOWNLOAD_DIR}/lib/libonnxruntime.so.${ONNXRUNTIME_VERSION}
    DESTINATION lib
)

ament_export_include_directories(include)
ament_export_libraries(onnxruntime)
ament_package()
