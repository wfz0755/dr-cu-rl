#########
# Setup #
#########

# Specify the minimum version for CMake
cmake_minimum_required(VERSION 3.1.0)

# Message
message(STATUS "ISPD19 DR CUHK")
message(STATUS ${CMAKE_CURRENT_SOURCE_DIR})

# Find includes in the build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Project's name
project(ispd19dr
        VERSION 2.1.0)

# Set the output folder where your program will be created
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR})

set(CMAKE_CXX_STANDARD 14)

set(PATH_RSYN ${CMAKE_CURRENT_SOURCE_DIR}/../rsyn)
set(PATH_ISPD18 ${CMAKE_CURRENT_SOURCE_DIR})

###################
# Warnings/Errors #
###################

set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -Werror=return-type")

######################
# Check Dependencies #
######################
set(Boost_USE_STATIC_LIBS ON)
find_package(Boost COMPONENTS filesystem program_options REQUIRED)

###############
# Source Code #
###############
file(GLOB_RECURSE SRC_FILES_RSYN
    ${PATH_RSYN}/src/*.cpp
    ${PATH_RSYN}/src/*.cc
    ${PATH_RSYN}/src/*.c)

file(GLOB_RECURSE SRC_FILES_ISPD18 ${PATH_ISPD18}/*.cpp)

set(SRC_FILES ${SRC_FILES_RSYN} ${SRC_FILES_ISPD18})

#################
# Library Paths #
#################
# Need to come before target is created.
find_library(DEF_LIB NAMES libdef.a PATHS ${PATH_RSYN}/lib/linux NO_DEFAULT_PATH)
find_library(LEF_LIB NAMES liblef.a PATHS ${PATH_RSYN}/lib/linux NO_DEFAULT_PATH)

###########
# Targets #
###########
add_library(ispd19dr ${SRC_FILES})
# target_link_libraries(ispd19dr ${SRC_FILES})

#######################
# Include Directories #
#######################

include_directories(${PATH_RSYN}/src)
include_directories(${PATH_RSYN}/src/rsyn/export)
include_directories(${PATH_RSYN}/include)
include_directories(${PATH_ISPD18})
#target_include_directories(ispd19dr
#        PUBLIC
#        .
#        ..
#        ${PATH_RSYN}/src
#        ${PATH_RSYN}/src/rsyn/export
#        ${PATH_RSYN}/include
#        ${PATH_ISPD18}
#        ${Boost_INCLUDE_DIR})
#################
# Linker Flags  #
#################

# TODO: set static under release mode only
set_target_properties(ispd19dr PROPERTIES LINK_FLAGS "-static -Wl,--whole-archive -rdynamic -lpthread -Wl,--no-whole-archive")

# Threads
# target_link_libraries(ispd19dr ${CMAKE_THREAD_LIBS_INIT})

# LEF/DEF
target_link_libraries(ispd19dr ${LEF_LIB})
target_link_libraries(ispd19dr ${DEF_LIB})

# Boost
target_include_directories(ispd19dr PUBLIC ${Boost_INCLUDE_DIR})
target_link_libraries(ispd19dr ${Boost_LIBRARIES})
