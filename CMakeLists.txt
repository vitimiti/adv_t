# ==================================================================================================
# The MIT License (MIT)
#
# Copyright (c) 2023 Victor Matia <vmatir@gmail.com>
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the “Software”), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.
# ==================================================================================================

cmake_minimum_required(VERSION 3.2)

option(VITIMITI_ADV_T_INSTALL_LIBRARY "Whether to install the library or not"
       OFF)

project(
  VITIMITI_ADV_T
  VERSION 1.0.1
  DESCRIPTION "Common advanced types for safety and ease of use"
  HOMEPAGE_URL "https://github.com/vitimiti/adv_t"
  LANGUAGES C CXX)

add_subdirectory(vendor)

add_library(${PROJECT_NAME} INTERFACE)
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_20)
target_include_directories(
  ${PROJECT_NAME}
  INTERFACE $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
            $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDE_DIR}>
            ${PREPROC_CONFIG_SOURCE_DIR}/include)

target_precompile_headers(${PROJECT_NAME} INTERFACE <cstdint> <optional>
                          <string_view> <variant>)

include(GNUInstallDirs)
if(VITIMITI_ADV_T_INSTALL_LIBRARY)
  message(TRACE
          "Configuring installation system with prefix ${CMAKE_INSTALL_PREFIX}")

  install(
    TARGETS VITIMITI_ADV_T
    EXPORT VITIMITI_ADV_T_TARGETS
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_LIBDIR})

  include(CMakePackageConfigHelpers)
  write_basic_package_version_file(
    "adv_t-config-version.cmake"
    VERSION ${VITIMITI_ADV_T_VERSION}
    COMPATIBILITY SameMajorVersion)

  configure_package_config_file(
    "${PROJECT_SOURCE_DIR}/cmake/vitimiti/adv_t-config.cmake.in"
    "${PROJECT_BINARY_DIR}/adv_t-config.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/vitimiti)

  install(
    EXPORT VITIMITI_ADV_T_TARGETS
    FILE vitimiti-adv_t-targets.cmake
    NAMESPACE ${PROJECT_NAME}::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/vitimiti)

  install(FILES "${PROJECT_BINARY_DIR}/adv_t-config.cmake"
                "${PROJECT_BINARY_DIR}/adv_t-config-version.cmake"
          DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/vitimiti)

  install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/vitimiti/adv_t
          DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
endif()
