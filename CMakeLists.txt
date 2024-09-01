cmake_minimum_required(VERSION 3.8)
project(onnxruntime_ament)

include(FetchContent)

# Define directories
set(ONNXRUNTIME_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX} CACHE STRING "ONNXRUNTIME install path")
set(ONNXRUNTIME_INCLUDE_DIR ${ONNXRUNTIME_INSTALL_PREFIX}/include)
set(ONNXRUNTIME_LIB_DIR ${ONNXRUNTIME_INSTALL_PREFIX}/lib)
set(ONNXRUNTIME_DOWNLOAD_DIR ${CMAKE_CURRENT_BINARY_DIR}/download)

# Create directories if they do not exist
file(MAKE_DIRECTORY ${ONNXRUNTIME_INCLUDE_DIR})
file(MAKE_DIRECTORY ${ONNXRUNTIME_LIB_DIR})
file(MAKE_DIRECTORY ${ONNXRUNTIME_DOWNLOAD_DIR})

FetchContent_Declare(onnxruntimeDownload
        URL https://github.com/microsoft/onnxruntime/releases/download/v1.7.0/onnxruntime-linux-x64-1.7.0.tgz
        UPDATE_COMMAND ""
        SOURCE_DIR ${ONNXRUNTIME_DOWNLOAD_DIR}
)
FetchContent_MakeAvailable(onnxruntimeDownload)

file(GLOB_RECURSE HEADERS "${ONNXRUNTIME_DOWNLOAD_DIR}/include/*.h")
foreach (HEADER_FILE ${HEADERS})
    message(STATUS "FOUND HEADER: " ${HEADER_FILE})
    file(COPY ${HEADER_FILE} DESTINATION ${ONNXRUNTIME_INCLUDE_DIR}/onnxruntime)
endforeach ()

find_package(ament_cmake REQUIRED)
find_library(onnxruntime_LIBRARY onnxruntime_ament PATHS ${ONNXRUNTIME_LIB_DIR})
add_library(onnxruntime_ament SHARED IMPORTED)
set_target_properties(onnxruntime_ament PROPERTIES
        IMPORTED_LOCATION ${onnxruntime_LIBRARY}
        INTERFACE_INCLUDE_DIRECTORIES ${ONNXRUNTIME_INCLUDE_DIR}
        LIBRARY_OUTPUT_PATH ${ONNXRUNTIME_LIB_DIR}
)

ament_package()
