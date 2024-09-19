# Set basic project properties
project(
  'fortran-yaml-c',
  ['fortran', 'c'], 
  version: '0.2.6', 
  license: 'GPL-2.0-or-later',
  license_files: 'LICENSE',
  default_options: [
    'fortran_std=f2018',
    'c_std=c18', 
    'b_staticpic=true', 
    'b_pie=true', 
    'warning_level=3', 
    'werror=false'])

pkg = import('pkgconfig')
enable_tests = get_option('enable_testing')

yaml_dep = dependency('yaml-0.1', method: 'pkg-config', required: true)

subdir('src')
# Build tests
if enable_tests
  subdir('tests')
endif
