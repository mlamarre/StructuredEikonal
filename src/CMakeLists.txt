cmake_minimum_required(VERSION 3.02)
project(Structured_Eikonal_Solver)

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

SET(BUILD_EXAMPLES false CACHE BOOL "Build sample executables")

find_package(CUDA REQUIRED)
message(STATUS "Found CUDA " ${CUDA_VERSION_MAJOR} "." ${CUDA_VERSION_MINOR} " in " ${CUDA_TOOLKIT_ROOT_DIR})

if (NOT DEFINED CUDA_COMPILE_ARCH_STR)
    set(CUDA_COMPILE_ARCH_STR "Auto")
endif()
cuda_select_nvcc_arch_flags(ARCH_FLAGS ${CUDA_COMPILE_ARCH_STR})

IF(NOT WIN32)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
ENDIF()
#END CMAKE SETTING CUDA_NVCC_FLAGS
###################
# Core / Examples
include_directories(${CMAKE_CURRENT_DIRECTORY})
include_directories(${CMAKE_SOURCE_DIR})
FILE(GLOB src cuda_fim.cu
     cuda_fim_kernel.cu
	 StructuredEikonal.cu
	 helper_timer.cpp)
FILE(GLOB hdr *.h)

CUDA_ADD_LIBRARY(STRUCTURED_EIKONAL ${src} ${hdr})
if (BUILD_EXAMPLES)
	CUDA_ADD_EXECUTABLE(Example1 example1.cu)
	TARGET_LINK_LIBRARIES(Example1 STRUCTURED_EIKONAL)
	if (CMAKE_SYSTEM_NAME MATCHES "Linux")
	  TARGET_LINK_LIBRARIES(Example1 -ldl)
	endif()
endif()

