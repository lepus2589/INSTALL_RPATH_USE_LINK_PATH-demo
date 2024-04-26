cmake_minimum_required(VERSION 3.25)

project(ProjectB VERSION 1.0.0 LANGUAGES CXX)

include(GNUInstallDirs)

add_library(B SHARED)

set_target_properties(
    B PROPERTIES
    VERSION ${ProjectB_VERSION}
    SOVERSION ${ProjectB_VERSION_MAJOR}
    DEBUG_POSTFIX "-debug"
)

target_sources(B PRIVATE "src/b.cpp")

target_sources(B PUBLIC
    FILE_SET HEADERS
    BASE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/src"
    FILES "src/b.h"
)

target_include_directories(B PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>"
    "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)

include(CMakePackageConfigHelpers)

set(ProjectB_INSTALL_CMAKEDIR "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}-${PROJECT_VERSION}")

install(
    TARGETS B
    EXPORT ProjectB_Targets
    FILE_SET HEADERS
)

install(
    EXPORT ProjectB_Targets
    DESTINATION "${ProjectB_INSTALL_CMAKEDIR}"
)

write_basic_package_version_file(
    ProjectBConfigVersion.cmake
    COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
    "ProjectBConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/ProjectBConfig.cmake"
    INSTALL_DESTINATION "${ProjectB_INSTALL_CMAKEDIR}"
)

install(
    FILES
    "${CMAKE_CURRENT_BINARY_DIR}/ProjectBConfigVersion.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/ProjectBConfig.cmake"
    DESTINATION "${ProjectB_INSTALL_CMAKEDIR}"
)
