cmake_minimum_required(VERSION 3.2)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(simple)

set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/..)

include_directories(${CMAKE_CURRENT_BINARY_DIR} ${SOURCE_DIR})

set(HEADER_FILES ${SOURCE_DIR}/rapidxml.hpp)

add_executable(${PROJECT_NAME} simple.cpp ${HEADER_FILES})

