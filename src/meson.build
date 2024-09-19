libyaml_interface_lib = static_library(
  'libyaml-interface', 
  'libyaml_interface.c',
  dependencies: [yaml_dep])

fortran_yaml_src = files([
  'fortran_yaml_c_types.f90', 
  'fortran_yaml_c_interface.f90', 
  'fortran_yaml_c.f90'])

fortran_yaml_c_lib = library(
  'fortran-yaml-c', 
  fortran_yaml_src,
  link_with: [libyaml_interface_lib], install: true)
pkg.generate(fortran_yaml_c_lib)

fortran_yaml_c_dep = declare_dependency(link_with: fortran_yaml_c_lib, dependencies: [yaml_dep])