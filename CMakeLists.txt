cmake_minimum_required(VERSION 3.21)

project(GameAnalytics)

set(GA_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/GA-SDK-CPP/source/gameanalytics")
set(DEPENDENCIES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/GA-SDK-CPP/source/dependencies")

#
# Collect source files.
#
file(
        GLOB_RECURSE CPP_SOURCES

        "${GA_SOURCE_DIR}/*.cpp"
        "${GA_SOURCE_DIR}/*.h"

        "${DEPENDENCIES_DIR}/crossguid/*.cpp"
        "${DEPENDENCIES_DIR}/stacktrace/*.cpp"
        "${DEPENDENCIES_DIR}/stacktrace/*.hpp"
        "${DEPENDENCIES_DIR}/sqlite/*.c"
        "${DEPENDENCIES_DIR}/sqlite/*.h"
        "${DEPENDENCIES_DIR}/crypto/*.c"
        "${DEPENDENCIES_DIR}/crypto/*.cpp"
        "${DEPENDENCIES_DIR}/zf_log/*.c"
        "${DEPENDENCIES_DIR}/zf_log/*.h"
        "${DEPENDENCIES_DIR}/miniz/*.c"
)

file(
        GLOB_RECURSE CPP_SOURCES_WIN32
        "${DEPENDENCIES_DIR}/stackwalker/*.cpp"
)

#
# We only care about building *some* shared libraries.
#
add_library(GameAnalytics SHARED)

target_sources(
        GameAnalytics
        PRIVATE
        ${CPP_SOURCES}
)

target_compile_definitions(
        GameAnalytics
        PUBLIC
        GA_SHARED_LIB
        CRYPTOPP_DISABLE_ASM
        USE_OPENSSL
        CURL_STATICLIB
)

target_include_directories(
        GameAnalytics
        PRIVATE
        "${DEPENDENCIES_DIR}/crossguid"
        "${DEPENDENCIES_DIR}/rapidjson"
        "${DEPENDENCIES_DIR}/stacktrace"
        "${DEPENDENCIES_DIR}/zf_log"
        "${DEPENDENCIES_DIR}/sqlite"
        "${DEPENDENCIES_DIR}/crypto"
        "${DEPENDENCIES_DIR}/miniz"
        "${DEPENDENCIES_DIR}/curl/include"
)

if (WIN32)
    target_sources(
            GameAnalytics
            PRIVATE
            ${CPP_SOURCES_WIN32}
    )
    target_compile_definitions(
            GameAnalytics
            PUBLIC
            GUID_WINDOWS
    )
    target_include_directories(
            GameAnalytics
            PRIVATE
            "${DEPENDENCIES_DIR}/stackwalker"
            "${DEPENDENCIES_DIR}/openssl/1.1.1d/include"
    )
    target_link_libraries(
            GameAnalytics
            ${DEPENDENCIES_DIR}/openssl/1.1.1d/libs/win64/vc140_x64_release/libeay32.lib
            ${DEPENDENCIES_DIR}/openssl/1.1.1d/libs/win64/vc140_x64_release/ssleay32.lib
            ${DEPENDENCIES_DIR}/curl/lib/win64/vc140_x64_release/libcurl.lib
    )
    target_compile_options(GameAnalytics PRIVATE /wd4838 /wd4858)
endif ()

if (UNIX AND NOT APPLE)
    target_compile_definitions(
            GameAnalytics
            PUBLIC
            GUID_LIBUUID
            USE_LINUX
    )
    target_include_directories(
            GameAnalytics
            PRIVATE
            #"${DEPENDENCIES_DIR}/ooid"
            "${DEPENDENCIES_DIR}/openssl/1.1.1d/include"
    )
    target_link_libraries(
            GameAnalytics
            ${DEPENDENCIES_DIR}/openssl/1.1.1d/libs/linux_x64/libcrypto.a
            ${DEPENDENCIES_DIR}/openssl/1.1.1d/libs/linux_x64/libssl.a
            ${DEPENDENCIES_DIR}/curl/lib/linux_x64/libcurl.a
    )
    target_compile_options(
            GameAnalytics
            PRIVATE
            $<$<CXX_COMPILER_ID:GNU>:-Wno-unused-result;-Wno-format-truncation>
            $<$<CXX_COMPILER_ID:Clang>:-Wno-implicit-const-int-float-conversion>
    )
    #set(CMAKE_POSITION_INDEPENDENT_CODE ON)
endif ()

target_include_directories(
        GameAnalytics
        PUBLIC
        "${GA_SOURCE_DIR}"
)
