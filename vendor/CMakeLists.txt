include(ExternalProject)

# External build for rlog library
find_program(MAKE_EXECUTABLE NAMES gmake make)
if(NOT MAKE_EXECUTABLE)
    message(FATAL_ERROR "GNU Make is required to build the external 'rlog' dependency.")
endif()

set(RLOG_INSTALL_DIR ${CMAKE_BINARY_DIR}/vendor/rlog-install)

# Potentially add this if you get file not found errors.
# file(MAKE_DIRECTORY "${RLOG_INSTALL_DIR}/include")

ExternalProject_Add(rlog_external
    URL               ${CMAKE_CURRENT_SOURCE_DIR}/rlog-1.4.tar.gz
    URL_HASH          SHA256=a938eeedeb4d56f1343dc5561bc09ae70b24e8f70d07a6f8d4b6eed32e783f79
    PREFIX            ${CMAKE_BINARY_DIR}/vendor/rlog-build
    BUILD_IN_SOURCE   1 
    PATCH_COMMAND     ${CMAKE_COMMAND} -E touch configure aclocal.m4 Makefile.in rlog/Makefile.in docs/Makefile.in config/Makefile.in
    CONFIGURE_COMMAND ./configure --prefix ${RLOG_INSTALL_DIR} --enable-static --disable-shared --disable-docs --disable-valgrind
    BUILD_COMMAND     ${MAKE_EXECUTABLE} -j4
    INSTALL_COMMAND   ${MAKE_EXECUTABLE} install
    LOG_CONFIGURE     1
    LOG_BUILD         1
    LOG_INSTALL       1
)

add_library(rlog STATIC IMPORTED)
set_target_properties(rlog PROPERTIES IMPORTED_LOCATION "${RLOG_INSTALL_DIR}/lib/librlog.a")
target_include_directories(rlog INTERFACE "${RLOG_INSTALL_DIR}/include")
add_dependencies(rlog rlog_external)