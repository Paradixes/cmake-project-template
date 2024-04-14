cmake_minimum_required(VERSION 3.25)

# ---------------------------------------------------------------------------------------
# Start project
# ---------------------------------------------------------------------------------------
include(cmake/FetchDependency.cmake)
include(cmake/GetGitVersion.cmake)

project(cmake-project-template LANGUAGES CXX)
get_git_version(GIT_VERSION)
set(PROJECT_VERSION ${GIT_VERSION})

message(STATUS "Build Project: ${PROJECT_NAME} ${PROJECT_VERSION}")

set(CUR_NAMESPACE ${PROJECT_NAME})
set(CUR_TARGET ${PROJECT_NAME})

# ---------------------------------------------------------------------------------------
# Set target library
# ---------------------------------------------------------------------------------------
add_library(${CUR_TARGET})
add_library(${CUR_NAMESPACE}::${CUR_TARGET} ALIAS ${CUR_TARGET})

set(CMAKE_CXX_STANDARD 17)

aux_source_directory(src SOURCES)

target_sources(${CUR_TARGET} PRIVATE ${SOURCES})

target_include_directories(${CUR_TARGET} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_link_libraries(${CUR_TARGET} PRIVATE spdlog::spdlog)