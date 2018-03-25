cmake_minimum_required(VERSION 3.2)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(rapidxml-devel VERSION 1.13 LANGUAGES CXX)

set(header_files
    ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml_iterators.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml_print.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml_utils.hpp)

add_library(${PROJECT_NAME} INTERFACE)

target_sources(${PROJECT_NAME} INTERFACE $<BUILD_INTERFACE:${header_files}>)

if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set (CMAKE_INSTALL_PREFIX "/usr" CACHE PATH "default install path" FORCE)
endif()

install(FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml_iterators.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml_print.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml_utils.hpp
    DESTINATION include/rapidxml)

add_subdirectory(test)

# Packaging
find_package(Git)
if(GIT_FOUND)
    execute_process(COMMAND ${GIT_EXECUTABLE} describe --abbrev=0 --tags
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        OUTPUT_VARIABLE VERSION_STRING
        OUTPUT_STRIP_TRAILING_WHITESPACE)

    if(VERSION_STRING)
        set(CPACK_PACKAGE_VERSION ${VERSION_STRING})
    else()
        set(CPACK_PACKAGE_VERSION "1.13")
    endif()
else()
    set(CPACK_PACKAGE_VERSION "1.13")
endif()

set(BUILD_NUM_FILE "${CMAKE_SOURCE_DIR}/build.num")
set(VERSION_FILE "${CMAKE_SOURCE_DIR}/build.version")
if (EXISTS ${BUILD_NUM_FILE})
    file(READ ${BUILD_NUM_FILE} BUILD_NUM)
else()
    set(BUILD_NUM 1)
endif()

if (EXISTS ${VERSION_FILE})
    file(READ ${VERSION_FILE} LAST_VERSION)
else()
    set(LAST_VERSION ${CPACK_PACKAGE_VERSION})
    file(WRITE ${VERSION_FILE} ${LAST_VERSION})
endif()

if (${LAST_VERSION} STREQUAL ${CPACK_PACKAGE_VERSION})
    math(EXPR BUILD_NUM ${BUILD_NUM}+1)
else()
    set(BUILD_NUM 1)
    set(LAST_VERSION ${CPACK_PACKAGE_VERSION})
    file(WRITE ${VERSION_FILE} ${LAST_VERSION})
endif()
file(WRITE ${BUILD_NUM_FILE} ${BUILD_NUM})

message(STATUS "Build num = " ${BUILD_NUM})
message(STATUS "Current Version = " ${CPACK_PACKAGE_VERSION})
message(STATUS "Install path = " ${CMAKE_INSTALL_PREFIX})

set(CPACK_GENERATOR "RPM")
set(CPACK_PACKAGE_NAME ${PROJECT_NAME})
set(CPACK_PACKAGE_RELEASE ${BUILD_NUM})
set(CPACK_PACKAGE_CONTACT "")
set(CPACK_PACKAGE_VENDOR "https://github.com/JoyIfBam5/rapidxml")
set(CPACK_PACKAGING_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX})
set(CPACK_PACKAGE_FILE_NAME
    "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${CPACK_PACKAGE_RELEASE}.${CMAKE_SYSTEM_PROCESSOR}")
set(CPACK_RPM_EXCLUDE_FROM_AUTO_FILELIST_ADDITION "/usr/share/cmake")
set(CPACK_RPM_PACKAGE_RELEASE ${BUILD_NUM})
set(CPACK_RPM_PACKAGE_SUMMARY "RapidXml is a fast XML DOM parser.")
set(CPACK_RPM_PACKAGE_LICENSE "Boost-1.0, MIT")
set(CPACK_RPM_PACKAGE_GROUP "Development/Libraries")
set(CPACK_RPM_PACKAGE_DESCRIPTION
"RapidXml is an attempt to create the fastest XML DOM parser possible,
while retaining useability, portability and reasonable W3C compatibility.
It is an in-situ parser written in C++, with parsing speed approaching that
of strlen() function executed on the same data.")
include(CPack)

