cmake_minimum_required(VERSION 3.15)

# Define the project version in a separate file
include(${CMAKE_CURRENT_SOURCE_DIR}/version.cmake)

# Define the project name and version
project(OpticLibrary VERSION ${PROJECT_VERSION} LANGUAGES CXX)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Set default build type if not specified
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build" FORCE)
endif()

# Options for building tests and benchmarks
option(BUILD_TESTS "Build the tests" ON)
option(BUILD_BENCHMARKS "Build the benchmarks" ON)

# Add compile options for different compilers
if(MSVC)
  add_compile_options(/W4 /WX /EHsc)
else()
  add_compile_options(-Wall -Wextra -Wpedantic -Werror)
endif()

# Sanitizer options
option(ENABLE_SANITIZER_ADDRESS "Enable address sanitizer" OFF)
option(ENABLE_SANITIZER_UNDEFINED_BEHAVIOR "Enable undefined behavior sanitizer" OFF)

# Option for building shared or static libraries
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)

# Set position-independent code flags for static libraries
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Define minimum versions for dependencies
set(GOOGLETEST_MIN_VERSION "1.12.0")
set(BENCHMARK_MIN_VERSION "1.6.0")

# Find dependencies
find_package(GTest ${GOOGLETEST_MIN_VERSION})
if(NOT GTest_FOUND)
  message(STATUS "GTest not found. Fetching from GitHub...")
  include(FetchContent)
  FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG release-1.12.1
  )
  FetchContent_MakeAvailable(googletest)
endif()

if(BUILD_BENCHMARKS)
  find_package(benchmark ${BENCHMARK_MIN_VERSION})
  if(NOT benchmark_FOUND)
    message(STATUS "Google Benchmark not found. Fetching from GitHub...")
    include(FetchContent)
    FetchContent_Declare(
      benchmark
      GIT_REPOSITORY https://github.com/google/benchmark.git
      GIT_TAG v1.8.2
    )
    FetchContent_MakeAvailable(benchmark)
  endif()
endif()

# Add subdirectories
add_subdirectory(data_structures)
add_subdirectory(algorithms)

# Configure sanitizers
if(ENABLE_SANITIZER_ADDRESS OR ENABLE_SANITIZER_UNDEFINED_BEHAVIOR)
  add_library(sanitizer_options INTERFACE)
  if(ENABLE_SANITIZER_ADDRESS)
    target_compile_options(sanitizer_options INTERFACE $<$<CONFIG:Debug>:-fsanitize=address>)
    target_link_options(sanitizer_options INTERFACE $<$<CONFIG:Debug>:-fsanitize=address>)
  endif()
  if(ENABLE_SANITIZER_UNDEFINED_BEHAVIOR)
    target_compile_options(sanitizer_options INTERFACE $<$<CONFIG:Debug>:-fsanitize=undefined>)
    target_link_options(sanitizer_options INTERFACE $<$<CONFIG:Debug>:-fsanitize=undefined>)
  endif()
endif()

# Add Doxygen for API documentation
find_package(Doxygen)
if(DOXYGEN_FOUND)
  set(DOXYGEN_IN ${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in)
  set(DOXYGEN_OUT ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)
  configure_file(${DOXYGEN_IN} ${DOXYGEN_OUT} @ONLY)

  # Ensure the output directory exists
  file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/docs/api)

  add_custom_target(doc_doxygen ALL
    COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYGEN_OUT}
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    COMMENT "Generating API documentation with Doxygen"
    VERBATIM)
  else()
    message(STATUS "Doxygen not found. API documentation will not be generated.")
  endif()

# Installation
include(CMakePackageConfigHelpers)
configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/OpticLibraryConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/OpticLibraryConfig.cmake"
    INSTALL_DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/OpticLibrary"
)
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/OpticLibraryConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)
install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/OpticLibraryConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/OpticLibraryConfigVersion.cmake"
    DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/OpticLibrary"
)
