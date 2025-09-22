
add_library(yaml_interface STATIC)
if (DOWNLOAD_LIBYAML)
  target_link_libraries(yaml_interface PRIVATE yaml)
else()
  target_link_libraries(yaml_interface PRIVATE PkgConfig::YAML)
endif()

target_sources(
  yaml_interface
  PRIVATE
  libyaml_interface.c
)

add_library(fortran-yaml-c)
target_sources(fortran-yaml-c
  PRIVATE
  fortran_yaml_c_types.f90 
  fortran_yaml_c_interface.f90
  fortran_yaml_c.f90 
)
target_link_libraries(fortran-yaml-c PRIVATE yaml_interface)
target_include_directories(
  fortran-yaml-c
  PUBLIC
  $<BUILD_INTERFACE:${CMAKE_Fortran_MODULE_DIRECTORY}>
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_LIBDIR}/fortran/${CMAKE_Fortran_COMPILER_ID}-${CMAKE_Fortran_COMPILER_VERSION}>
)

if ("${CMAKE_Fortran_COMPILER_ID}" MATCHES "GNU")
  target_compile_options(fortran-yaml-c PRIVATE -fimplicit-none)
  if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_options(fortran-yaml-c PRIVATE -Wunused -fcheck=all)
  endif()
endif()

