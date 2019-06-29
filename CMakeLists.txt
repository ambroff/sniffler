cmake_minimum_required(VERSION 3.13)

project(sniffler CXX)

# If conan is being used, configure CMake to use conan for dependencies.
include(${CMAKE_BINARY_DIR}/conan_paths.cmake OPTIONAL)

set(Boost_USE_STATIC_LIBS ON)
if (CONAN_BOOST_ROOT)
  set(BOOST_ROOT "${CONAN_BOOST_ROOT}")
endif()
find_package(Boost 1.70 REQUIRED COMPONENTS system)

add_library(sniffler INTERFACE)

target_include_directories(sniffler INTERFACE include)

target_link_libraries(sniffler INTERFACE
  Boost::system
  )

enable_testing()
add_executable(sniffler_tests tests/sniffler_tests.cpp)
set_property(TARGET sniffler_tests PROPERTY CXX_STANDARD 20)
target_link_libraries(sniffler_tests PRIVATE sniffler)
target_compile_options(sniffler_tests PRIVATE -Wall -Werror -Wextra -Wpedantic -fconcepts)

find_program(CLANG_FORMAT_EXE NAMES clang-format)
if (CLANG_FORMAT_EXE)
  file(GLOB_RECURSE ALL_HPP_INCLUDE_FILES ${CMAKE_SOURCE_DIR}/*.hpp.in)
  file(GLOB_RECURSE ALL_HPP_FILES ${CMAKE_SOURCE_DIR}/*.hpp)
  file(GLOB_RECURSE ALL_CPP_FILES ${CMAKE_SOURCE_DIR}/*.cpp)
  set(ALL_SOURCE_AND_HEADER_FILES ${ALL_HPP_FILES} ${ALL_HPP_INCLUDE_FILES} ${ALL_CPP_FILES})
  add_custom_target(format COMMAND ${CLANG_FORMAT_EXE} -i ${ALL_SOURCE_AND_HEADER_FILES})
endif()
