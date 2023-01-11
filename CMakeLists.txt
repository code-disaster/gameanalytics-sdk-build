cmake_minimum_required(VERSION 3.21)

project(GameAnalytics)

set(GA_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/GA-SDK-CPP/source/gameanalytics")
set(DEPENDENCIES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/GA-SDK-CPP/source/dependencies")

add_definitions("-DGA_SHARED_LIB")
add_definitions("-DGUID_WINDOWS")
add_definitions("-DCRYPTOPP_DISABLE_ASM")
add_definitions("-DUSE_OPENSSL -DCURL_STATICLIB")

file(
        GLOB_RECURSE CPP_SOURCES

        "${GA_SOURCE_DIR}/*.cpp"
        "${GA_SOURCE_DIR}/*.h"

        "${DEPENDENCIES_DIR}/crossguid/*.cpp"
        "${DEPENDENCIES_DIR}/stacktrace/*.cpp"
        "${DEPENDENCIES_DIR}/stacktrace/*.hpp"
        "${DEPENDENCIES_DIR}/stackwalker/*.cpp"
        "${DEPENDENCIES_DIR}/sqlite/*.c"
        "${DEPENDENCIES_DIR}/sqlite/*.h"
        "${DEPENDENCIES_DIR}/crypto/*.c"
        "${DEPENDENCIES_DIR}/crypto/*.cpp"
        "${DEPENDENCIES_DIR}/zf_log/*.c"
        "${DEPENDENCIES_DIR}/zf_log/*.h"
        "${DEPENDENCIES_DIR}/miniz/*.c"
)

include_directories(
        "${DEPENDENCIES_DIR}/crossguid"
        "${DEPENDENCIES_DIR}/rapidjson"
        "${DEPENDENCIES_DIR}/stacktrace"
        "${DEPENDENCIES_DIR}/stackwalker"
        "${DEPENDENCIES_DIR}/zf_log"
        "${DEPENDENCIES_DIR}/sqlite"
        "${DEPENDENCIES_DIR}/crypto"
        "${DEPENDENCIES_DIR}/miniz"
        "${DEPENDENCIES_DIR}/curl/include"
)

add_library(GameAnalytics SHARED ${CPP_SOURCES})

include_directories(
        "${DEPENDENCIES_DIR}/openssl/1.1.1d/include"
)

target_link_libraries(
        GameAnalytics
        ${DEPENDENCIES_DIR}/openssl/1.1.1d/libs/win64/vc140_x64_release/libeay32.lib
        ${DEPENDENCIES_DIR}/openssl/1.1.1d/libs/win64/vc140_x64_release/ssleay32.lib
        ${DEPENDENCIES_DIR}/curl/lib/win64/vc140_x64_release/libcurl.lib
        ws2_32.lib
)

target_include_directories(
        GameAnalytics
        PUBLIC
        "${GA_SOURCE_DIR}"
)
