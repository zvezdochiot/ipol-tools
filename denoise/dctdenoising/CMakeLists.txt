cmake_minimum_required(VERSION 2.8)
project(dctdenoising)

# The build type "Release" adds some optimizations
if (NOT CMAKE_BUILD_TYPE)
  set (CMAKE_BUILD_TYPE "Release")
endif ()

# GCC on MacOs needs this option to use the clang assembler
if ((CMAKE_CXX_COMPILER_ID STREQUAL "GNU") AND (APPLE))
  set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wa,-q")
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wa,-q")
  set (CMAKE_INCLUDE_SYSTEM_FLAG_C "-isystem")
  set (CMAKE_INCLUDE_SYSTEM_FLAG_CXX "-isystem")
endif ()

# Optimize to the current CPU and enable warnings
if ((CMAKE_CXX_COMPILER_ID STREQUAL "GNU") OR
(CMAKE_CXX_COMPILER_ID STREQUAL "Clang") OR
(CMAKE_CXX_COMPILER_ID STREQUAL "AppleClang"))
  set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=native -Wall -Wextra")
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native -Wall -Wextra")
endif ()

# Enable C99
if (CMAKE_VERSION VERSION_LESS "3.1")
  set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")
else ()
  set (CMAKE_C_STANDARD 99)
endif ()

# Enable C++11
if (CMAKE_VERSION VERSION_LESS "3.1")
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")
else ()
  set (CMAKE_CXX_STANDARD 11)
endif ()

# Enable OpenMP
find_package (OpenMP)
if(OPENMP_FOUND)
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
  set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
endif()

# Link LibFFTW
find_path (FFTW_INCLUDE_DIR fftw3.h)
find_library (FFTWF_LIBRARIES NAMES fftw3f)
include_directories (SYSTEM ${FFTW_INCLUDE_DIR})
link_libraries (${FFTWF_LIBRARIES})
if (NOT FFTW_INCLUDE_DIR)
  message (FATAL_ERROR "FFTW3 not found.")
endif ()
find_path (IIO_INCLUDE_DIR iio.h)
find_library (IIO_LIBRARIES NAMES iio)
include_directories (SYSTEM ${iio_INCLUDE_DIR})
link_libraries (${IIO_LIBRARIES})
if (NOT IIO_INCLUDE_DIR)
  message (FATAL_ERROR "IIO not found.")
endif ()

set(SOURCE_FILES
    main.cpp
    DCTdenoising.cpp
    DCTdenoising.h
    DCTPatch.hpp
    Image.hpp
    utils.cpp
    utils.hpp
    demoutils.cpp
    demoutils.hpp
)

add_executable(dctdenoising ${SOURCE_FILES})
