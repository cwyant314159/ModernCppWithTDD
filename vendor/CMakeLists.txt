include(ExternalProject)

#
# CppUTest
#
set(CPPUTEST_EXTENSIONS ON CACHE BOOL "" FORCE)
set(CPPUTEST_MEM_LEAK_DETECTION_DISABLED OFF CACHE BOOL "" FORCE)
set(CPPUTEST_BUILD_TESTING OFF CACHE BOOL "" FORCE)
set(CPPUTEST_EXAMPLES OFF CACHE BOOL "" FORCE)
add_subdirectory(cpputest)

#
# Google Test
#
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
set(INSTALL_GTEST OFF CACHE BOOL "" FORCE)
add_subdirectory(googletest)

#
# cURL
#
set(HTTP_ONLY ON CACHE BOOL "" FORCE)
set(BUILD_CURL_EXE OFF CACHE BOOL "" FORCE)
set(BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(BUILD_TESTING OFF CACHE BOOL "" FORCE)
set(BUILD_LIBCURL_DOCS OFF CACHE BOOL "" FORCE)
set(BUILD_MISC_DOCS OFF CACHE BOOL "" FORCE)
set(ENABLE_CURL_MANUAL OFF CACHE BOOL "" FORCE)
set(BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)
set(BUILD_STATIC_LIBS ON CACHE BOOL "" FORCE)
set(CURL_DISABLE_LDAP ON CACHE BOOL "" FORCE)
set(CURL_DISABLE_TELNET ON CACHE BOOL "" FORCE)
set(CURL_DISABLE_DICT ON CACHE BOOL "" FORCE)
set(CURL_USE_LIBPSL OFF CACHE BOOL "" FORCE)
add_subdirectory(curl)

option(JSONCPP_WITH_TESTS "Compile and (for jsoncpp_check) run JsonCpp test executables" ON)
option(JSONCPP_WITH_POST_BUILD_UNITTEST "Automatically run unit-tests as a post build step" ON)
option(JSONCPP_WITH_WARNING_AS_ERROR "Force compilation to fail if a warning occurs" OFF)
option(JSONCPP_WITH_STRICT_ISO "Issue all the warnings demanded by strict ISO C and ISO C++" ON)
option(JSONCPP_WITH_PKGCONFIG_SUPPORT "Generate and install .pc files" ON)
option(JSONCPP_WITH_CMAKE_PACKAGE "Generate and install cmake package files" ON)
option(JSONCPP_WITH_EXAMPLE "Compile JsonCpp example" OFF)
option(JSONCPP_WITH_INSTALL "Include JsonCpp header and binaries in the install target" ON)

#
# JsonCpp
#
set(JSONCPP_WITH_TESTS OFF CACHE BOOL "" FORCE)
set(JSONCPP_WITH_POST_BUILD_UNITTEST OFF CACHE BOOL "" FORCE)
set(JSONCPP_WITH_WARNING_AS_ERROR OFF CACHE BOOL "" FORCE)
set(JSONCPP_WITH_PKGCONFIG_SUPPORT OFF CACHE BOOL "" FORCE)
set(JSONCPP_WITH_CMAKE_PACKAGE OFF CACHE BOOL "" FORCE)
set(JSONCPP_WITH_EXAMPLE OFF CACHE BOOL "" FORCE)
set(JSONCPP_WITH_INSTALL OFF CACHE BOOL "" FORCE)
set(BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)
set(BUILD_STATIC_LIBS ON CACHE BOOL "" FORCE)
set(BUILD_OBJECT_LIBS OFF CACHE BOOL "" FORCE)
add_subdirectory(jsoncpp)

#
# rlog
#
set(RLOG_INSTALL_DIR ${CMAKE_BINARY_DIR}/vendor/rlog-install)

ExternalProject_Add(rlog_external
    URL               ${CMAKE_CURRENT_SOURCE_DIR}/rlog-1.4.tar.gz
    URL_HASH          SHA256=a938eeedeb4d56f1343dc5561bc09ae70b24e8f70d07a6f8d4b6eed32e783f79
    PREFIX            ${CMAKE_BINARY_DIR}/vendor/rlog-build
    BUILD_IN_SOURCE   1
    PATCH_COMMAND     ${CMAKE_COMMAND} -E touch configure aclocal.m4 Makefile.in rlog/Makefile.in docs/Makefile.in config/Makefile.in
    CONFIGURE_COMMAND ./configure --prefix ${RLOG_INSTALL_DIR} --enable-static --disable-shared --disable-docs --disable-valgrind
    BUILD_COMMAND     make -j4
    INSTALL_COMMAND   make install
    LOG_CONFIGURE     1
    LOG_BUILD         1
    LOG_INSTALL       1
)

add_library(rlog STATIC IMPORTED)
set_target_properties(rlog PROPERTIES IMPORTED_LOCATION "${RLOG_INSTALL_DIR}/lib/librlog.a")
target_include_directories(rlog INTERFACE "${RLOG_INSTALL_DIR}/include")
add_dependencies(rlog rlog_external)