cmake_minimum_required(VERSION 3.10)

project(pro)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -w -g -O0 -fPIC -fopenmp -pthread")

set(CUDA_HOME $ENV{CUDA_HOME})
set(SYS_LIB $ENV{SYS_LIB})
set(CPP_PKG $ENV{CPP_PKG})

# Opencv & CUDA & other libraries
find_package(OpenCV REQUIRED)
find_package(CUDA REQUIRED)

# Include directories
include_directories(${CMAKE_SOURCE_DIR}/src
                    ${CUDA_HOME}/include/cuda
                    ${CUDA_HOME}/include/tensorRT
                    ${CPP_PKG}/opencv2/include
                    ${CUDA_HOME}/include/protobuf
                    ${PROTOBUF_INCLUDE_DIR}
                    )

# Library directories
link_directories(${CUDA_HOME}/lib64 
                 ${SYS_LIB} 
                 ${CPP_PKG}/opencv4.2/lib)

# Source files
file(GLOB_RECURSE CPP_SRC_FILES "src/*.cpp")
file(GLOB_RECURSE CU_SRC_FILES "src/*.cu")

# Setup CUDA compile options
set(CUDA_NVCC_FLAGS "${CUDA_NVCC_FLAGS} -std=c++11 -w -g -O0 -Xcompiler \"${CMAKE_CXX_FLAGS}\"")
cuda_add_executable(${PROJECT_NAME} ${CPP_SRC_FILES} ${CU_SRC_FILES})

# Link libraries
target_link_libraries(${PROJECT_NAME} 
                      cudart cudnn 
                      nvinfer nvinfer_plugin 
                      stdc++ dl protobuf 
                      opencv_core opencv_imgproc opencv_imgcodecs
                      pthread)

# Set RPATH for shared libraries
set_target_properties(${PROJECT_NAME} PROPERTIES INSTALL_RPATH_USE_LINK_PATH TRUE)

# Installation (optional)
install(TARGETS ${PROJECT_NAME} DESTINATION workspace)