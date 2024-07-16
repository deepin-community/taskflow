# This is an amalgam of the Taskflow top-level CMakeLists.txt and the
# unittests one.

# CMake version
cmake_minimum_required (VERSION 3.18)
MESSAGE(STATUS "CMAKE_ROOT: " ${CMAKE_ROOT})

# Project name
project(TaskflowTests VERSION 3.6.0 LANGUAGES CXX)

# Turn on the verbose
set(CMAKE_VERBOSE_MAKEFILE ON)

# Enable test
include(CTest)

# Find packages
set(CMAKE_SYSTEM_PREFIX_PATH "/usr")
find_package(Threads REQUIRED)
find_package(Taskflow REQUIRED)

add_library(${PROJECT_NAME} INTERFACE)
target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_17)
target_link_libraries(${PROJECT_NAME} INTERFACE Taskflow::Taskflow)
target_include_directories(${PROJECT_NAME} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

enable_testing()

include(/usr/lib/cmake/doctest/doctest.cmake)

list(APPEND TF_UNITTESTS 
  test_utility 
  test_work_stealing 
  #test_serializer 
  test_priorities
  test_basics 
  test_asyncs
  test_dependent_asyncs
  test_subflows
  test_control_flow
  test_semaphores
  test_movable
  test_cancellation
  test_for_each
  test_reduce
  test_transform
  test_sort
  test_scan
  test_find
  test_compositions
  test_traversals
  test_pipelines
  test_scalable_pipelines
  test_deferred_pipelines
  test_deferred_scalable_pipelines
  test_runtimes
  test_data_pipelines
)

foreach(unittest IN LISTS TF_UNITTESTS)
  add_executable(${unittest} ${unittest}.cpp)
  target_link_libraries(${unittest} ${PROJECT_NAME} Taskflow::Taskflow)
  target_include_directories(${unittest} PRIVATE /usr/lib/cmake/doctest /usr/include/doctest)
  doctest_discover_tests(${unittest})
endforeach()

# include CUDA tests
if(TF_BUILD_CUDA)
  add_subdirectory(${TF_UTEST_DIR}/cuda)
endif()

# include SYCL tests
if(TF_BUILD_SYCL)
  add_subdirectory(${TF_UTEST_DIR}/sycl)
endif()
