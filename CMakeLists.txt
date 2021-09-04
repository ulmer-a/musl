cmake_minimum_required(VERSION 3.14)

project(musl_libc VERSION 1.2.2)

set(TARGET_ARCH "x86_64")
enable_language(ASM)


file(GLOB_RECURSE LIBC_SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/*.h
    ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/*.S
)

set(CMAKE_C_FLAGS "-ffreestanding -nostdinc -Os -fPIC -fexcess-precision=standard \
    -frounding-math -Wa,--noexecstack -pipe -fomit-frame-pointer -fno-unwind-tables \
    -fno-asynchronous-unwind-tables -ffunction-sections -fdata-sections \
    -Wno-pointer-to-int-cast")

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
add_dependencies(musl alltypes.h syscall.h)

add_custom_target(alltypes.h
    COMMAND mkdir -p ${CMAKE_CURRENT_BINARY_DIR}/include/bits
    COMMAND sed -f ${CMAKE_CURRENT_SOURCE_DIR}/tools/mkalltypes.sed
        ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/bits/alltypes.h.in
        ${CMAKE_CURRENT_SOURCE_DIR}/include/alltypes.h.in
        > ${CMAKE_CURRENT_BINARY_DIR}/include/bits/alltypes.h
    COMMENT include/bits/alltypes.h
    BYPRODUCTS ${CMAKE_CURRENT_BINARY_DIR}/include/bits/alltypes.h
    SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/bits/alltypes.h.in
        ${CMAKE_CURRENT_SOURCE_DIR}/include/alltypes.h.in
)

add_custom_target(syscall.h
    COMMAND mkdir -p ${CMAKE_CURRENT_BINARY_DIR}/include/bits
    COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/bits/syscall.h.in
        ${CMAKE_CURRENT_BINARY_DIR}/include/bits/syscall.h
    COMMAND sed -n -e s/__NR_/SYS_/p < ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/bits/syscall.h.in
        >> ${CMAKE_CURRENT_BINARY_DIR}/include/bits/syscall.h
    COMMENT include/bits/syscall.h
    BYPRODUCTS ${CMAKE_CURRENT_BINARY_DIR}/include/bits/syscall.h
    SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/arch/${TARGET_ARCH}/bits/syscall.h.in
)
