cmake_minimum_required(VERSION 2.8.12)
project(pyransac)

add_definitions(-DA64)
add_definitions(-std=c++11)
add_definitions(-fPIC)



FIND_PACKAGE(OpenMP)
if (OPENMP_FOUND)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_CXX_FLAGS}")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
endif (OPENMP_FOUND)

message("Looking for OpenCV")
FIND_PACKAGE(OpenCV 2.4.9 REQUIRED )
if (OpenCV_FOUND)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenCV_CXX_FLAGS}")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenCV_CXX_FLAGS}")
  include_directories(${OpenCV_INDLUDE_DIRS})
else(OpenCV_FOUND)
  message("OpenCV not found!")
endif (OpenCV_FOUND)

enable_language (Fortran)
FIND_PACKAGE(LAPACK REQUIRED)
if (LAPACK_FOUND)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${LAPACK_CXX_FLAGS}")
endif (LAPACK_FOUND)

SET(CMAKE_BUILD_TYPE "RELEASE")

IF(CMAKE_COMPILER_IS_GNUCXX)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wno-strict-aliasing")
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wextra -Wno-write-strings")
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated -ansi")
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -ftree-vectorize -funroll-loops")
ENDIF(CMAKE_COMPILER_IS_GNUCXX)

add_subdirectory(src/pyransac/matutls)
add_subdirectory(lib/pybind11)


####### DEGENSAC
include_directories(
src/pyransac
src/pyransac/degensac)
set(degensac_srcs
        src/pyransac/degensac/DegUtils.c
        src/pyransac/degensac/exp_ranF.c
        src/pyransac/degensac/exp_ranH.c
        src/pyransac/degensac/Ftools.c
        src/pyransac/degensac/hash.c
        src/pyransac/degensac/Htools.c
        src/pyransac/degensac/ranF.c
        src/pyransac/degensac/ranH2el.c
        src/pyransac/degensac/ranH.c
        src/pyransac/degensac/rtools.c
        src/pyransac/degensac/utools.c
        src/pyransac/degensac/lapwrap.c
)


pybind11_add_module(pyransac "src/pyransac/bindings.cpp" ${degensac_srcs})
target_link_libraries(pyransac PRIVATE  ${OpenCV_LIBS} ${LAPACK_LIBRARIES} matutls)
