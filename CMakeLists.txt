cmake_minimum_required(VERSION 2.6 FATAL_ERROR)

project(maple_lcm_dist)

# generate JSON file of compile commands -- useful for code extension
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

#--------------------------------------------------------------------------------------------------
# Configurable options:

option(STATIC_BINARIES "Link binaries statically." ON)
option(USE_SORELEASE   "Use SORELEASE in shared library filename." ON)

add_compile_options( -g)
add_compile_options( -pthread )

add_compile_options("$<$<CONFIG:RELWITHDEBINFO>:-O3>")
add_compile_options("$<$<CONFIG:RELWITHDEBINFO>:-mtune=native>")

add_compile_options("$<$<CONFIG:RELEASE>:-O3>")
add_compile_options("$<$<CONFIG:RELEASE>:-g0>")
add_compile_options("$<$<CONFIG:RELEASE>:-DNDEBUG>")
add_compile_options("$<$<CONFIG:RELEASE>:-mtune=native>")

add_compile_options("$<$<CONFIG:DEBUG>:-O0>")


#--------------------------------------------------------------------------------------------------
# Library version:

set(MAPLE_LCM_DIST_SOMAJOR   2)
set(MAPLE_LCM_DIST_SOMINOR   1)
set(MAPLE_LCM_DIST_SORELEASE 0)

# Compute VERSION and SOVERSION:
if (USE_SORELEASE)
  set(MAPLE_LCM_DIST_VERSION ${MAPLE_LCM_DIST_SOMAJOR}.${MAPLE_LCM_DIST_SOMINOR}.${MAPLE_LCM_DIST_SORELEASE})
else()
  set(MAPLE_LCM_DIST_VERSION ${MAPLE_LCM_DIST_SOMAJOR}.${MAPLE_LCM_DIST_SOMINOR})
endif()
set(MAPLE_LCM_DIST_SOVERSION ${MAPLE_LCM_DIST_SOMAJOR})

#--------------------------------------------------------------------------------------------------
# Dependencies:

find_package(ZLIB)
include_directories(${ZLIB_INCLUDE_DIR})
include_directories(${maple_lcm_dist_SOURCE_DIR}/minisat)

#--------------------------------------------------------------------------------------------------
# Compile flags:

add_definitions(-D__STDC_FORMAT_MACROS -D__STDC_LIMIT_MACROS)

#--------------------------------------------------------------------------------------------------
# Build Targets:

set(MAPLE_LCM_DIST_LIB_SOURCES
    minisat/utils/Options.cc
    minisat/utils/System.cc
    minisat/core/Solver.cc
    minisat/simp/SimpSolver.cc)

add_library(maple_lcm_dist-lib-static STATIC ${MAPLE_LCM_DIST_LIB_SOURCES})
add_library(maple_lcm_dist-lib-shared SHARED ${MAPLE_LCM_DIST_LIB_SOURCES})

target_link_libraries(maple_lcm_dist-lib-shared ${ZLIB_LIBRARY})
target_link_libraries(maple_lcm_dist-lib-static ${ZLIB_LIBRARY})

add_executable(maple_lcm_dist_core minisat/core/Main.cc)
add_executable(maple_lcm_dist_simp minisat/simp/Main.cc)

if(STATIC_BINARIES)
  target_link_libraries(maple_lcm_dist_core maple_lcm_dist-lib-static)
  target_link_libraries(maple_lcm_dist_simp maple_lcm_dist-lib-static)
else()
  target_link_libraries(maple_lcm_dist_core maple_lcm_dist-lib-shared)
  target_link_libraries(maple_lcm_dist_simp maple_lcm_dist-lib-shared)
endif()

set_target_properties(maple_lcm_dist-lib-static PROPERTIES OUTPUT_NAME "maple_lcm_dist")
set_target_properties(maple_lcm_dist-lib-shared
  PROPERTIES
    OUTPUT_NAME "maple_lcm_dist"
    VERSION ${MAPLE_LCM_DIST_VERSION}
    SOVERSION ${MAPLE_LCM_DIST_SOVERSION})

set_target_properties(maple_lcm_dist_simp       PROPERTIES OUTPUT_NAME "maple_lcm_dist")

#--------------------------------------------------------------------------------------------------
# Installation targets:

install(TARGETS maple_lcm_dist-lib-static maple_lcm_dist-lib-shared maple_lcm_dist_core maple_lcm_dist_simp
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)

install(DIRECTORY minisat/mtl minisat/utils minisat/core minisat/simp
        DESTINATION include/maple_lcm_dist
        FILES_MATCHING PATTERN "*.h")
