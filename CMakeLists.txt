# This file is part of MARIE.
#
# MARIE is free software: you can redistribute it and/or modify it under the
# terms of the GNU General Public License as published by the Free Software
# Foundation, either version 3 of the License, or (at your option) any later
# version.
#
# MARIE is distributed in the hope that it will be useful, but WITHOUT ANY
# WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR
# A PARTICULAR PURPOSE. See the GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along with
# MARIE. If not, see <https://www.gnu.org/licenses/>.

# Build system structure is heavily influenced by
# https://cliutils.gitlab.io/modern-cmake/.

# The FATAL_ERROR option is accepted but ignored by CMake 2.6 and higher. It
# should be specified so CMake versions 2.4 and lower fail with an error
# instead of just a warning.
cmake_minimum_required(VERSION 3.7...3.23 FATAL_ERROR)

if (${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif ()

project(MARIE
        VERSION 1.0.0.0
        DESCRIPTION "Multi Arcade Retro Interactive Environment"
        HOMEPAGE_URL "https://github.com/kaichiuchi/MARIE"
        LANGUAGES CXX)

# Set the CMake module path.
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

# Enforce out-of-source builds.
file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" LOC_PATH)

if (EXISTS "${LOC_PATH}")
    message(FATAL_ERROR "You cannot build in a source directory (or any directo"
            "ry with a CMakeLists.txt file). Please make a build su"
            "bdirectory. Feel free to remove CMakeCache.txt and CMa"
            "keFiles.")
endif ()

# If a build type has not been defined, we set the build type here.
#
# For development versions, this should be set to Debug. For all other versions,
# this should be set to RelWithDebInfo.
#
# This only matters for single-target generators, and has no effect on
# multi-target generators.
#
# https://www.kitware.com/cmake-and-the-default-build-type/
set(MARIE_DEFAULT_BUILD_TYPE "Debug")

if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "Setting build type to '${MARIE_DEFAULT_BUILD_TYPE}'"
            "as none was specified.")
    set(CMAKE_BUILD_TYPE "${MARIE_DEFAULT_BUILD_TYPE}" CACHE
            STRING "Choose the type of build." FORCE)
    # Set the possible values of build type for cmake-gui
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
            "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif ()

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

option(MARIE_BENCHMARKS_BUILD "Build the benchmarks" OFF)
option(MARIE_TESTS_BUILD "Build the unit tests" OFF)