cmake_minimum_required(VERSION 3.21)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Geode's macOS platform supports a universal binary. This includes the
# Apple Silicon arm64 slice as well as the Intel x86_64 slice.
if ("${CMAKE_SYSTEM_NAME}" STREQUAL "iOS" OR IOS)
    set(CMAKE_OSX_ARCHITECTURES "arm64")
elseif (APPLE)
    set(CMAKE_OSX_ARCHITECTURES "arm64;x86_64")
endif()

set(CMAKE_CXX_VISIBILITY_PRESET hidden)

project(BufferMod VERSION 1.0.0)

# Compile the source from src/, matching the standard Geode project layout.
file(GLOB_RECURSE SOURCES CONFIGURE_DEPENDS src/*.cpp)
if (NOT SOURCES)
    message(FATAL_ERROR "No source files found in src/. Expected src/main.cpp.")
endif()

add_library(${PROJECT_NAME} SHARED ${SOURCES})

if (NOT DEFINED ENV{GEODE_SDK})
    message(FATAL_ERROR "Unable to find Geode SDK! Please define GEODE_SDK environment variable to point to Geode")
else()
    message(STATUS "Found Geode: $ENV{GEODE_SDK}")
endif()

add_subdirectory($ENV{GEODE_SDK} ${CMAKE_CURRENT_BINARY_DIR}/geode)

setup_geode_mod(${PROJECT_NAME})
