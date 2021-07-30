cmake_minimum_required(VERSION 2.8.6)
project(SeamAwareDecimater)

cmake_policy(SET CMP0021 OLD)
cmake_policy(SET CMP0054 OLD)

SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

## We need libigl
find_package(LIBIGL REQUIRED)
include_directories( "${LIBIGL_INCLUDE_DIR}" )

## We need Eigen
find_package(EIGEN REQUIRED)
include_directories( "${EIGEN_INCLUDE_DIR}" )

## We don't have/want MOSEK
add_definitions(-DIGL_NO_MOSEK)

link_directories(
	/usr/local/lib
	${EIGEN_DIRS}
) 

## We need C++11. Put this directive after CGAL's include.
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -g " )

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
  add_compile_options(-stdlib=libc++)
endif()

## MSVC requires "standards conformance mode"
# if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
#   add_compile_options(/Za)
# endif()

# Avoid licensing issues - eigen optionally uses LGPL code, which we want to avoid
# See ext/eigen/COPYING.README for details
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DEIGEN_MPL2_ONLY")

add_library(DEC_LIBS
    OBJECT
    quadric_error_metric.cpp
    cost_and_placement.cpp
    half_edge.cpp
    neighbor_faces_and_boundary.cpp
    detect_foldover.cpp
    decimate.cpp
    collapse_edge_seam.cpp
    )
    
add_executable(decimater
	decimater.cpp
	$<TARGET_OBJECTS:DEC_LIBS>
	)
target_link_libraries ( decimater
	${LIBIGL_LIBRARIES}
)
