cmake_minimum_required(VERSION 3.14)

project(musl_libc VERSION 1.2.2)

set(TARGET_ARCH "x86_64")
enable_language(ASM)

set(LIBC_INCLUDE
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_BINARY_DIR}/include
)

file(GLOB_RECURSE LIBC_SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/*.h
    ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/*.S
)

set(CMAKE_C_FLAGS "-ffreestanding -nostdinc -Os -fPIC \
    -frounding-math -Wa,--noexecstack -pipe -fomit-frame-pointer -fno-unwind-tables \
    -fno-asynchronous-unwind-tables -ffunction-sections -fdata-sections \
    -Wno-shift-op-parentheses -Wno-unknown-pragmas -Wno-parentheses -Wno-string-plus-int \
    -Wno-ignored-attributes")

add_library(musl STATIC
    ${LIBC_SOURCES}
)
target_include_directories(musl PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/src/include
    ${CMAKE_CURRENT_SOURCE_DIR}/src/internal
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_BINARY_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}
    ${CMAKE_CURRENT_SOURCE_DIR}/arch/generic
)
target_compile_definitions(musl PRIVATE
    VERSION="${CMAKE_PROJECT_VERSION}"
    _XOPEN_SOURCE=700
)
