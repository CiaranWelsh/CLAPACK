cmake_minimum_required(VERSION 2.6)
project(CLAPACK C)

if (WIN32 AND NOT CYGWIN)
    set(SECOND_SRC ${CLAPACK_SOURCE_DIR}/INSTALL/winsecond.c)
    set(DSECOND_SRC ${CLAPACK_SOURCE_DIR}/INSTALL/windsecnd.c)
    add_definitions(-DNO_ISATTY -DMSDOS -DUSE_CLOCK)
else ()
    set(SECOND_SRC ${CLAPACK_SOURCE_DIR}/INSTALL/second.c)
    set(DSECOND_SRC ${CLAPACK_SOURCE_DIR}/INSTALL/dsecnd.c)
endif ()

#option(BUILD_F2C_ARITHCHK "build the arithchk target of f2c" ON)
option(USE_BLAS_WRAP "pre-pend f2c_ to each function in blas" OFF)
option(BUILD_TESTING "build the clapack tests" OFF)
if (NOT USE_BLAS_WRAP)
    # _zrotg_ seems to be missing in the wrap header
    add_definitions(-DNO_BLAS_WRAP)
endif ()
include_directories(${CLAPACK_SOURCE_DIR}/INCLUDE)

add_subdirectory(F2CLIBS)
add_subdirectory(BLAS)
add_subdirectory(SRC)

if (${BUILD_TESTING})
    enable_testing()
    include(CTest)
    add_subdirectory(TESTING)
endif ()

set(CLAPACK_VERSION 3.2.1)
set(CPACK_PACKAGE_VERSION_MAJOR 3)
set(CPACK_PACKAGE_VERSION_MINOR 2)
set(CPACK_PACKAGE_VERSION_PATCH 1)
include(CPack)
export(TARGETS f2c blas lapack FILE clapack-targets.cmake)
configure_file(${CLAPACK_SOURCE_DIR}/clapack-config-version.cmake.in
               ${CLAPACK_BINARY_DIR}/clapack-config-version.cmake @ONLY)
configure_file(${CLAPACK_SOURCE_DIR}/clapack-config.cmake.in
               ${CLAPACK_BINARY_DIR}/clapack-config.cmake @ONLY)

install(TARGETS f2c blas lapack
        EXPORT clapack
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/clapack
        )

# install the export info
install(
        EXPORT clapack
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/clapack
        NAMESPACE CLAPACK::
        FILE clapack.cmake
)















