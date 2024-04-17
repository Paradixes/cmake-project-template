cmake_minimum_required(VERSION 3.11)
project(my_project_utests CXX)

if (NOT TARGET my_project)
    # Stand-alone build
    find_package(my_project REQUIRED)
endif ()

find_package(Catch2 3 QUIET)
if (Catch2_FOUND)
    message(STATUS "Packaged version of Catch will be used.")
else ()
    message(STATUS "Bundled version of Catch will be downloaded and used.")
    include(FetchContent)
    FetchContent_Declare(Catch2
            GIT_REPOSITORY https://github.com/catchorg/Catch2.git
            GIT_TAG 53d0d913a422d356b23dd927547febdf69ee9081 # v3.5.0
    )
    FetchContent_MakeAvailable(Catch2)
endif ()

set(MY_PROJECT_UTESTS_SOURCES
        main.cpp
        test_case_1.cpp
)

enable_testing()

add_executable(my-project-utests ${MY_PROJECT_UTESTS_SOURCES})
target_link_libraries(my-project-utests PRIVATE my_project::my_project)
target_link_libraries(my-project-utests PRIVATE Catch2::Catch2WithMain)
add_test(NAME my-project-utests COMMAND my-project-utests)
set_tests_properties(my-project-utests PROPERTIES RUN_SERIAL ON)