
add_library(libyaml_interface libyaml_interface.c)
if (DOWNLOAD_LIBYAML)
  target_link_libraries(libyaml_interface PUBLIC yaml)
else()
  target_link_libraries(libyaml_interface PUBLIC PkgConfig::YAML)
endif()

set(fortran_yaml_src
        fortran_yaml_c_types.f90
        fortran_yaml_c_interface.f90
        fortran_yaml_c.f90
)

add_library(fortran-yaml-c ${fortran_yaml_src})
target_link_libraries(fortran-yaml-c PUBLIC libyaml_interface)

# Store generated mod-files in a separate folder
set(moddir "${CMAKE_CURRENT_BINARY_DIR}/mod")
set_target_properties(fortran-yaml-c PROPERTIES
        Fortran_MODULE_DIRECTORY "${moddir}"
)

# Add mod-file directory to include search path for targets depending on the library
target_include_directories(fortran-yaml-c PUBLIC
        $<BUILD_INTERFACE:${moddir}>
        $<INSTALL_INTERFACE:${INSTALL_MODULEDIR}>
)

# Install the library
install(TARGETS fortran-yaml-c libyaml_interface
        EXPORT fortran-yaml-c-targets
        LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        PUBLIC_HEADER DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/${INSTALL_INCLUDEDIR}"
)

# Install the folder with the modfiles (needed by projects using the library)
install(DIRECTORY "${moddir}/" DESTINATION "${INSTALL_MODULEDIR}")
