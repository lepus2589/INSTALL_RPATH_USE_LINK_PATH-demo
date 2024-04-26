cmake_minimum_required(VERSION 3.25)

project(ProjectA VERSION 1.0.0 LANGUAGES CXX)

include(GNUInstallDirs)

# find_package(ProjectB REQUIRED CONFIG HINTS "${CMAKE_CURRENT_SOURCE_DIR}/3rdparty/install")
find_package(ProjectB REQUIRED CONFIG)

add_library(A SHARED)

set_target_properties(
    A PROPERTIES
    VERSION ${ProjectA_VERSION}
    SOVERSION ${ProjectA_VERSION_MAJOR}
    DEBUG_POSTFIX "-debug"
    INSTALL_RPATH_USE_LINK_PATH TRUE
)

target_sources(A PRIVATE "src/a.cpp")

target_sources(A PUBLIC
    FILE_SET HEADERS
    BASE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/src"
    FILES "src/a.h"
)

target_link_libraries(A PUBLIC B)

target_include_directories(A PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>"
    "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)

include(CMakePackageConfigHelpers)

set(ProjectA_INSTALL_CMAKEDIR "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}-${PROJECT_VERSION}")

install(
    TARGETS A
    EXPORT ProjectA_Targets
    FILE_SET HEADERS
)

install(
    EXPORT ProjectA_Targets
    DESTINATION "${ProjectA_INSTALL_CMAKEDIR}"
)

write_basic_package_version_file(
    ProjectAConfigVersion.cmake
    COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
    "ProjectAConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/ProjectAConfig.cmake"
    INSTALL_DESTINATION "${ProjectA_INSTALL_CMAKEDIR}"
    PATH_VARS ProjectB_DIR
)

install(
    FILES
    "${CMAKE_CURRENT_BINARY_DIR}/ProjectAConfigVersion.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/ProjectAConfig.cmake"
    DESTINATION "${ProjectA_INSTALL_CMAKEDIR}"
)
