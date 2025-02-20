cmake_minimum_required(VERSION 3.0 FATAL_ERROR)

set(CMAKE_BUILD_TYPE Release)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_COLOR_MAKEFILE ON)
set(VERBOSE_BUILD ON)
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
set(CMAKE_MACOSX_RPATH TRUE)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

project(gputk)

set(current_dir "${CMAKE_CURRENT_SOURCE_DIR}")

include(${current_dir}/sources.cmake)
include(CheckCXXCompilerFlag)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
else()
    message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

set(GPUTKLIB_STATIC ${GPUTKLIB})
set(GPUTKLIB_SHARED lib${GPUTKLIB})

# Create static library
add_library(${GPUTKLIB_STATIC} STATIC ${LIBGPUTK_SOURCE_FILES})
set_property(TARGET ${GPUTKLIB_STATIC} PROPERTY CXX_STANDARD 11)
set_target_properties(${GPUTKLIB_STATIC} PROPERTIES ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# Create shared library
add_library(${GPUTKLIB_SHARED} SHARED ${LIBGPUTK_SOURCE_FILES})
set_property(TARGET ${GPUTKLIB_SHARED} PROPERTY CXX_STANDARD 11)
if (UNIX)
    set_target_properties(${GPUTKLIB_SHARED} PROPERTIES OUTPUT_NAME ${GPUTKLIB_STATIC})
    set_target_properties(${GPUTKLIB_SHARED} PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
endif (UNIX)

# Install both static and shared libraries
install(TARGETS ${GPUTKLIB_STATIC} ${GPUTKLIB_SHARED}
    RUNTIME DESTINATION libgputk/bin
    LIBRARY DESTINATION libgputk/lib
    ARCHIVE DESTINATION libgputk/lib
)

# Install headers
install(DIRECTORY src/
    DESTINATION libgputk/include
    FILES_MATCHING PATTERN "*.h"
                 PATTERN "*.hpp"
)

install(DIRECTORY third_party/
    DESTINATION libgputk/include
    FILES_MATCHING PATTERN "*.h"
                 PATTERN "*.hpp"
)

# Install the required third-party headers
install(DIRECTORY third_party/nlohmann/
    DESTINATION nlohmann
    FILES_MATCHING PATTERN "*.h"
                 PATTERN "*.hpp"
)