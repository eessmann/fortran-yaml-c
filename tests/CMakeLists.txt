set(YAML_TESTS test_yaml example)

foreach(YAML_TEST ${YAML_TESTS})
  add_executable(${YAML_TEST} "${YAML_TEST}.f90")
  target_link_libraries(${YAML_TEST} PRIVATE fortran-yaml-c)
endforeach()