cmake_minimum_required(VERSION 3.28..3.30)
project (FORTRAN_YAML_C VERSION "0.2.6" LANGUAGES C Fortran)

include(GNUInstallDirs)
include(FortranCInterface)
FortranCInterface_VERIFY()

set(CMAKE_Fortran_MODULE_DIRECTORY "${CMAKE_BINARY_DIR}/modules")

option(DOWNLOAD_LIBYAML "If ON, then libyaml will be downloaded and 
built." ON)

# Find libyaml
if (DOWNLOAD_LIBYAML) 
  # use CPM to download and build libyaml
  include(cmake/CPM.cmake)
  CPMAddPackage(
    NAME libyaml 
    VERSION 0.2.5
    GITHUB_REPOSITORY "yaml/libyaml"
    GIT_TAG "release/0.2.5"
    PATCHES cmake/cmake_fix.patch
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

add_subdirectory(src)
add_subdirectory(tests)

install(
  TARGETS fortran-yaml-c yaml_interface
  COMPONENT fortran-yaml-c
  EXPORT fortran-yaml-c-targets
)

set(fortran_yaml_c_INSTALL_CONFIGDIR "${CMAKE_INSTALL_LIBDIR}/cmake/fortran-yaml-c")
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/fortran-yaml-cConfigVersion.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY ExactVersion
)

configure_package_config_file(
  "${CMAKE_CURRENT_SOURCE_DIR}/cmake/fortran-yaml-c-config.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/fortran-yaml-c-config.cmake"
  INSTALL_DESTINATION "${fortran_yaml_c_INSTALL_CONFIGDIR}"
)

install(
  DIRECTORY ${CMAKE_Fortran_MODULE_DIRECTORY}
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/fortran/${CMAKE_Fortran_COMPILER_ID}-${CMAKE_Fortran_COMPILER_VERSION}
)

install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/fortran-yaml-c-config.cmake"
  "${CMAKE_CURRENT_BINARY_DIR}/fortran-yaml-cConfigVersion.cmake"
  DESTINATION "${fortran_yaml_c_INSTALL_CONFIGDIR}"
)

install(
  EXPORT fortran-yaml-c-targets
  FILE "fortran-yaml-c-targets.cmake"
  NAMESPACE fortran_yaml::
  DESTINATION "${fortran_yaml_c_INSTALL_CONFIGDIR}"
)