cmake_minimum_required(VERSION 3.8)
project(onnxruntime_ament)

find_package(ament_cmake REQUIRED)

# Define directories, and create directories if they do not exist
set(ONNXRUNTIME_DOWNLOAD_DIR ${CMAKE_CURRENT_SOURCE_DIR})

set(ONNXRUNTIME_VERSION 1.11.0)

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
