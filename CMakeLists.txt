cmake_minimum_required(VERSION 3.12)

project(csvpp
  LANGUAGES CXX
  DESCRIPTION "C++/Boost-based library for reading and writing CSV files"
  VERSION 1.0.1)

set(CMAKE_CXX_STANDARD 20)

include(GNUInstallDirs)
include(CheckIncludeFiles)
include(CMakePackageConfigHelpers)

find_package(Boost REQUIRED)
include_directories(${Boost_INCLUDE_DIRS})

file(GLOB_RECURSE src
     src/*.cpp)

add_library(csvpp STATIC ${src})

target_include_directories(csvpp PUBLIC  
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>  
    $<INSTALL_INTERFACE:include>
)
configure_file(csvpp.pc.in csvpp.pc @ONLY)

configure_package_config_file(
  "${CMAKE_CURRENT_SOURCE_DIR}/csvpp-config.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/csvpp-config.cmake"
  INSTALL_DESTINATION
    "{CMAKE_INSTALL_LIBDIR}/cmake/csvpp"
  NO_SET_AND_CHECK_MACRO
  NO_CHECK_REQUIRED_COMPONENTS_MACRO
)

install(TARGETS csvpp
        EXPORT csvpp-targets
        RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
        ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")

install(EXPORT csvpp-targets
        NAMESPACE csvpp::
        DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/csvpp"
        EXPORT_LINK_INTERFACE_LIBRARIES)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/csvpp-config.cmake"
        DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/csvpp")

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/csvpp.pc DESTINATION lib/pkgconfig)
install(DIRECTORY src/csv DESTINATION include)
