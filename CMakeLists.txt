cmake_minimum_required(VERSION 3.25)

# ---------------------------------------------------------------------------------------
# Start project
# ---------------------------------------------------------------------------------------
include(cmake/FetchDependency.cmake)
include(cmake/GetGitVersion.cmake)

project(my_project LANGUAGES CXX)
get_git_version(GIT_VERSION)
set(PROJECT_VERSION ${GIT_VERSION})

set(CUR_NAMESPACE my_project)
set(CUR_TARGET my_project)
message(STATUS "Build Project: ${PROJECT_NAME} ${PROJECT_VERSION}")

# ---------------------------------------------------------------------------------------
# Compiler config
# ---------------------------------------------------------------------------------------
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# make sure __cplusplus is defined when using msvc and enable parallel build
if (MSVC)
    string(APPEND CMAKE_CXX_FLAGS " /Zc:__cplusplus /MP")
endif ()

set(CMAKE_CXX_EXTENSIONS OFF)

if (CMAKE_SYSTEM_NAME MATCHES "CYGWIN" OR CMAKE_SYSTEM_NAME MATCHES "MSYS" OR CMAKE_SYSTEM_NAME MATCHES "MINGW")
    set(CMAKE_CXX_EXTENSIONS ON)
endif ()

# ---------------------------------------------------------------------------------------
# Configure options
# ---------------------------------------------------------------------------------------
# test option
option(MY_PROJECT_BUILD_TESTS "Build tests" OFF)

# clang-tidy
option(MY_PROJECT_CLANG_TIDY "run clang tidy in this project" OFF)

if (MY_PROJECT_CLANG_TIDY)
    set(CMAKE_CXX_CLANG_TIDY "clang-tidy")
    set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
    message(STATUS "Enabled clang-tidy")
endif ()

# ---------------------------------------------------------------------------------------
# Set target library
# ---------------------------------------------------------------------------------------
add_library(${CUR_TARGET})
add_library(${CUR_NAMESPACE}::${CUR_TARGET} ALIAS ${CUR_TARGET})

aux_source_directory(src SOURCES)

target_sources(${CUR_TARGET} PRIVATE ${SOURCES})

target_include_directories(${CUR_TARGET} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_link_libraries(${CUR_TARGET} PRIVATE spdlog::spdlog)

# ---------------------------------------------------------------------------------------
# Add Tests
# ---------------------------------------------------------------------------------------
if (MY_PROJECT_BUILD_TESTS)
    message(STATUS "Enable testing")
    enable_testing()
    add_subdirectory(tests)
endif ()