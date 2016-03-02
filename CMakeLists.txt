cmake_minimum_required(VERSION 3.4)

project(sodium VERSION 1.0.8)

include(ExternalProject)

find_program(MAKE make)

ExternalProject_Add(${PROJECT_NAME}
    PREFIX ${CMAKE_CURRENT_BINARY_DIR}
    URL https://github.com/jedisct1/libsodium/releases/download/${PROJECT_VERSION}/libsodium-${PROJECT_VERSION}.tar.gz
    URL_MD5 0a66b86fd3aab3fe4c858edcd2772760
    INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}
    CONFIGURE_COMMAND ./configure
        --prefix=<INSTALL_DIR>
        --enable-static
        --disable-shared
    BUILD_IN_SOURCE 1
    INSTALL_COMMAND ${MAKE} install
)

ExternalProject_Get_Property(${PROJECT_NAME} INSTALL_DIR)

string(TOLOWER "${PROJECT_NAME}" PROJECT_NAME_LOWER)

configure_file(config.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME_LOWER}-config.cmake"
    @ONLY)

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME_LOWER}-config-version.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)


install(DIRECTORY
    "${INSTALL_DIR}/include"
    DESTINATION "lib/${CMAKE_LIBRARY_ARCHITECTURE}/${PROJECT_NAME_LOWER}-${PROJECT_VERSION}"
)

install(DIRECTORY
    "${INSTALL_DIR}/lib"
    DESTINATION "lib/${CMAKE_LIBRARY_ARCHITECTURE}/${PROJECT_NAME_LOWER}-${PROJECT_VERSION}"
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME_LOWER}-config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME_LOWER}-config-version.cmake"
    DESTINATION "lib/${CMAKE_LIBRARY_ARCHITECTURE}/${PROJECT_NAME_LOWER}-${PROJECT_VERSION}"
)

export(PACKAGE ${PROJECT_NAME})

set(CPACK_GENERATOR "TBZ2")
set(CPACK_PACKAGE_FILE_NAME "${PROJECT_NAME_LOWER}-${PROJECT_VERSION}-${CMAKE_LIBRARY_ARCHITECTURE}")
include(CPack)
