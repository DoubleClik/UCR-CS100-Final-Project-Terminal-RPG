cmake_minimum_required(VERSION 3.15)
project(rpg)

set(CMAKE_CXX_STANDARD 20)

set(CMAKE_CXX_FLAGS --coverage)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/bin")

# Reference: https://github.com/google/googletest/tree/main/googletest#incorporating-into-an-existing-cmake-project
# Download and unpack googletest at configure time
configure_file(CMakeLists.txt.in googletest-download/CMakeLists.txt)
execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
        RESULT_VARIABLE result
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download)
if (result)
    message(FATAL_ERROR "CMake step for googletest failed: ${result}")
endif ()
execute_process(COMMAND ${CMAKE_COMMAND} --build .
        RESULT_VARIABLE result
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download)
if (result)
    message(FATAL_ERROR "Build step for googletest failed: ${result}")
endif ()

# Prevent overriding the parent project's compiler/linker
# settings on Windows
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# Add googletest directly to our build. This defines
# the gtest and gtest_main targets.
add_subdirectory(${CMAKE_CURRENT_BINARY_DIR}/googletest-src
        ${CMAKE_CURRENT_BINARY_DIR}/googletest-buildif
        EXCLUDE_FROM_ALL)

# The gtest/gtest_main targets carry header search path
# dependencies automatically when using CMake 2.8.11 or
# later. Otherwise we have to add them here ourselves.
if (CMAKE_VERSION VERSION_LESS 2.8.11)
    include_directories("${gtest_SOURCE_DIR}/include")
endif ()

set(SOURCE_FILES
    src/equipment/armor.cpp
    src/equipment/weapon.cpp

    src/enemy.cpp
    src/fightEncounter.cpp
    src/fightSystem.cpp
    src/game.cpp
    src/helperFunctions.cpp
    src/player.cpp
    src/prompter.cpp)

add_executable(play main.cpp ${SOURCE_FILES})

set(TEST_FILES test/enemyTest.cpp test/playerTest.cpp test/equipmentTest.cpp test/helperFunctionsTest.cpp test/fightEncounterTest.cpp)

add_executable(test ${TEST_FILES} ${SOURCE_FILES})

target_link_libraries(test gtest gtest_main)
target_include_directories(test PUBLIC header)

target_include_directories(play PUBLIC header)