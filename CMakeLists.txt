### ---[ PCL global CMake
cmake_minimum_required(VERSION 3.8 FATAL_ERROR)

project(POST_0 LANGUAGES CUDA CXX)

find_package(CUDA 10 REQUIRED)
message(STATUS ${CUDA_INCLUDE_DIRS})
message(STATUS ${CUDA_TOOLKIT_INCLUDE})

message(STATUS ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES})

enable_language(CUDA)

# set(CUDA_TOOLKIT_ROOT_DIR "/usr/local/cuda-10.0/")

include(CheckLanguage)
check_language(CUDA)

if(NOT DEFINED CMAKE_CUDA_STANDARD)
    set(CMAKE_CUDA_STANDARD 11)
    set(CMAKE_CUDA_STANDARD_REQUIRED ON)
endif()

if(POLICY CMP0074)
  # 1. Remove with 3.12.4.
  # 2. Remove search paths with *_ROOT since they will be automatically checked
  cmake_policy(SET CMP0074 NEW)
endif()

# Set target C++ standard and required compiler features
set(CMAKE_CXX_STANDARD 14 CACHE STRING "The target C++ standard. PCL requires C++14 or higher.")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# if(CMAKE_VERSION VERSION_LESS 3.8)
  # CMake did not have cxx_std_14 compile feature prior to 3.8
  # We use cxx_attribute_deprecated as a proxy because this feature is a part of c++14 standard
#  set(PCL_CXX_COMPILE_FEATURES cxx_attribute_deprecated)
# else()

  set(PCL_CXX_COMPILE_FEATURES cxx_std_14)

# endif()

add_compile_options("-O3")

find_package(PCL 1.11 REQUIRED)


option(BUILD_CUDA "Build the CUDA-related subsystems" ${DEFAULT})

# if(NOT (BUILD_CUDA AND CUDA_FOUND))
#   return()
# endif()

if(CMAKE_COMPILER_IS_GNUCXX)
  string(REPLACE "-Wold-style-cast" "" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
  string(REPLACE "-Wno-invalid-offsetof" "" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-conversion -Wno-unused-parameter -Wno-unused-variable -Wno-unused-function -Wno-unused-but-set-variable")
endif()

include_directories(SYSTEM "${CUDA_INCLUDE_DIRS}/include/")
# include_directories(SYSTEM "${CUDA_INCLUDE_DIRS}/../../")
# or probably better
# include_directories(SYSTEM "${CUDA_TOOLKIT_INCLUDE}/../../")

include_directories(${PCL_INCLUDE_DIRS} ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

# set_target_properties( ${PROJECT_NAME} 
#                        PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

# set_source_files_properties( main.cpp
set_source_files_properties( main_cpu.cpp
    PROPERTIES
    COMPILE_FLAGS "-O3 -DNDEBUG" )

# set_target_properties(${PROJECT_NAME} PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

add_executable(${PROJECT_NAME} "main.cpp")
target_link_libraries (${PROJECT_NAME} ${PCL_LIBRARIES})


