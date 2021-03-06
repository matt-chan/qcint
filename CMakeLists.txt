cmake_minimum_required (VERSION 2.6)
project (qcint)
set(qcint_VERSION "1.8")

set(CMAKE_BUILD_TYPE RELWITHDEBINFO)
set(CMAKE_VERBOSE_MAKEFILE OFF)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -msse3 -O2 -funroll-loops -ftree-vectorize -ffast-math")

include(GNUInstallDirs)

#set(CMAKE_INCLUDE_CURRENT_DIR ON)

#enable_language(Fortran)
find_package(BLAS REQUIRED)
set(CMAKE_REQUIRED_INCLUDES math.h)
set(CMAKE_REQUIRED_LIBRARIES m)
check_function_exists(expl HAVE_EXPL)
check_function_exists(sqrtl HAVE_SQRTL)

include_directories(
  ${PROJECT_BINARY_DIR}/include
  ${PROJECT_SOURCE_DIR}/src
  ${PROJECT_BINARY_DIR}/src)

configure_file(
  "${PROJECT_SOURCE_DIR}/src/config.h.in"
  "${PROJECT_BINARY_DIR}/src/config.h")

configure_file(
  "${PROJECT_SOURCE_DIR}/include/cint.h.in"
  "${PROJECT_BINARY_DIR}/include/cint.h")

set(cintSrc 
  src/c2f.c src/cart2sph.c src/cint1e.c src/cint2e.c src/cint_bas.c
  src/fblas.c src/g1e.c src/g2e_2d4d.c src/g2e_g02d.c src/g2e_init.c
  src/g2e_lj4d.c src/g2e_ssss.c src/gout2e_sse3.c src/misc.c
  src/optimizer.c src/rys_roots.c
  src/cint2c2e.c src/g2c2e.c src/cint3c2e.c src/g3c2e.c src/optimizer_3c2e.c 
  src/cint3c1e.c src/g3c1e.c src/breit.c
  src/autocode/auto_intor1.c src/autocode/auto_intor2.c
  src/autocode/auto_intor3.c src/autocode/grad1.c
  src/autocode/grad2.c src/autocode/grad3.c src/autocode/auto_hess.c
  src/autocode/auto_3c2e.c src/autocode/auto_3c1e.c
  src/autocode/gaunt1.c src/autocode/breit1.c)


set(BUILD_SHARED_LIBS 1)
if(ENABLE_STATIC)
  option(BUILD_SHARED_LIBS 0)
endif()

add_library(cint ${cintSrc})
set_target_properties(cint PROPERTIES
  VERSION ${qcint_VERSION}
  LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR})
target_link_libraries(cint ${BLAS_LIBRARIES})

install(TARGETS cint DESTINATION ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR})
install(FILES ${PROJECT_BINARY_DIR}/include/cint.h DESTINATION ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_INCLUDEDIR})
