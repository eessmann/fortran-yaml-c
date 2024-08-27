cmake_minimum_required(VERSION 3.20)

# Set basic project properties
project(fortran-yaml-c
        VERSION "0.2.6"
        DESCRIPTION "This is YAML parser for Fortran matching the YAML 1.1 spec."
        LANGUAGES C Fortran
)

include(FortranCInterface)
FortranCInterface_VERIFY()

# Set up install directories
include(GNUInstallDirs)
set(INSTALL_INCLUDEDIR "${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}")
set(INSTALL_MODULEDIR "${INSTALL_INCLUDEDIR}/mod")

option(DOWNLOAD_LIBYAML "If ON, then libyaml will be downloaded and built." ON)
option(FORTRAN_YAML_C_TESTING ON)
include(cmake/CPM.cmake)
# Find libyaml
if (DOWNLOAD_LIBYAML)
  # use CPM to download and build libyaml
  CPMAddPackage(
          NAME libyaml
          VERSION 0.2.5
          GITHUB_REPOSITORY "yaml/libyaml"
          GIT_TAG "release/0.2.5"
          EXCLUDE_FROM_ALL ON
  )
else()
  # Try to find libyaml using pkg-config
  find_package(PkgConfig REQUIRED)
  pkg_check_modules(YAML REQUIRED IMPORTED_TARGET yaml-0.1)
  if (NOT (${YAML_VERSION} STREQUAL "0.2.5"))
    message(FATAL_ERROR "PkgConfig found yaml version ${YAML_VERSION} but version 0.2.5 is required")
  endif()
endif()

# Include project specific CMake functions
include(cmake/fortran-yaml-c.cmake)

# Include user customizable config settings
include(config.cmake)

# Configure the build type (see cmake/fortran-yaml-c.cmake)
configure_build_type()

# Configure the compiler flags (see cmake/fortran-yaml-c.cmake)
configure_compiler_flags()

# Build library
add_subdirectory(src)

# Build tests
if(FORTRAN_YAML_C_TESTING)
  enable_testing()
  add_subdirectory(tests)
endif()

#
# Install package config find, so that other CMake project can find this project
#
include(CMakePackageConfigHelpers)

# If project uses customized finders, they should be installed with it
if(EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules)
  install(
          DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules
          DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/fortran-yaml-c
  )
endif()

# Install project, add namespace
install(
        EXPORT fortran-yaml-c-targets
        FILE "fortran-yaml-c-targets.cmake"
        NAMESPACE "fortran-yaml-c::"
        DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/fortran-yaml-c"
)

# Create and install CMake package config file
configure_package_config_file(
        "${CMAKE_CURRENT_SOURCE_DIR}/cmake/export/fortran-yaml-c-config.cmake.in"
        "${CMAKE_CURRENT_BINARY_DIR}/cmake/fortran-yaml-c-config.cmake"
        INSTALL_DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/fortran-yaml-c"
)
write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/cmake/fortran-yaml-c-config-version.cmake"
        VERSION "${PROJECT_VERSION}"
        COMPATIBILITY SameMajorVersion
)
install(
        FILES "${CMAKE_CURRENT_BINARY_DIR}/cmake/fortran-yaml-c-config.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/cmake/fortran-yaml-c-config-version.cmake"
        DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/fortran-yaml-c"
)