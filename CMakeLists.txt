set(TARGET_NAME "PEGTL")

#=============================================================================
# CMake integration Copyright 2015 Christian Frisson.
#
# Distributed under the OSI-approved BSD License (the "License");
# see accompanying file Copyright.txt for details.
#
# This software is distributed WITHOUT ANY WARRANTY; without even the
# implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
# See the License for more information.
#=============================================================================

project(PEGTL)

cmake_minimum_required(VERSION 3.0.0)

######################
#       OPTIONS      #
######################

option(BUILD_SHARED_LIBS "Build ${TARGET_NAME} libraries as shared" OFF)
option(USE_DEBUG "Use Debug" ON)
option(BUILD_DOCUMENTATION "Build ${TARGET_NAME} reference documentation using doxygen (use: make documentation)" OFF)
option(BUILD_APPS "Build apps" OFF)

if(USE_DEBUG)
	message( "\nConfigured for Debug Build")
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DUSE_DEBUG -DTI_DEBUG=1")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DUSE_DEBUG -DTI_DEBUG=1")
	if(NOT WIN32)
		set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g")
		set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g")
	endif()
	set(CMAKE_BUILD_TYPE "Debug")
	set(CMAKE_VERBOSE_MAKEFILE ON)
else(USE_DEBUG)
	message( "\nConfigured for Release Build")
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DTI_DEBUG=0 -O3")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DTI_DEBUG=0 -O3")
	set(CMAKE_BUILD_TYPE "MinSizeRel")#Debug|Release|RelWithDebInfo|MinSizeRel
	set(CMAKE_VERBOSE_MAKEFILE ON)#CF
	if(APPLE)
		set(CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/release")
	endif()
endif(USE_DEBUG)

#########################
# INTERNAL DEPENDENCIES #
#########################

# Core dependencies
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

######################
# INTERNAL LIBRARIES #
######################

set(CMAKE_CXX_FLAGS "-std=c++1y ${CMAKE_CXX_FLAGS}")

file(GLOB_RECURSE ${TARGET_NAME}_SRC pegtl/*.cc)
file(GLOB_RECURSE ${TARGET_NAME}_HDR pegtl/*.hh)

add_library(${TARGET_NAME} INTERFACE)# ${${TARGET_NAME}_SRC} ${${TARGET_NAME}_HDR} pegtl.hh)
target_sources(${TARGET_NAME} INTERFACE ${${TARGET_NAME}_HDR} ${CMAKE_CURRENT_SOURCE_DIR}/pegtl.hh)

######################
#         APPS       #
######################

if(BUILD_APPS)
include_directories(${CURRENT_SOURCE_DIR}/examples)
file(GLOB ${TARGET_NAME}_DEMO_SRCS examples/*.cc)
foreach(${TARGET_NAME}_DEMO_SRC ${${TARGET_NAME}_DEMO_SRCS})
	get_filename_component(${TARGET_NAME}_DEMO ${${TARGET_NAME}_DEMO_SRC} NAME_WE)
	#message("${TARGET_NAME} demo src ${${TARGET_NAME}_DEMO}")
	add_executable(${TARGET_NAME}-example-${${TARGET_NAME}_DEMO} ${${TARGET_NAME}_DEMO_SRC})
	target_link_libraries(${TARGET_NAME}-example-${${TARGET_NAME}_DEMO} ${TARGET_NAME})
endforeach()
endif()

######################
#        TESTS       #
######################

#add_subdirectory(test) #TODO

message("[X] ${TARGET_NAME}")
