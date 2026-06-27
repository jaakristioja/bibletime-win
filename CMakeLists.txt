
project(bibletime-win)
cmake_minimum_required(VERSION 3.17)
include(ExternalProject)
Set(CMAKE_CONFIGURATION_TYPES "Release" CACHE STRING "" FORCE)
Set(BT_DOC_REV v3.2.0)
Set(PROJECT_INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}/install)



ExternalProject_Add(
    bibletime-docs
    GIT_REPOSITORY https://github.com/bibletime/generated_docs.git
    GIT_TAG master
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
    INSTALL_COMMAND ""
)
ExternalProject_Get_Property(bibletime-docs SOURCE_DIR)
Set(bibletime-docs_SOURCE_DIR ${SOURCE_DIR})
MESSAGE(STATUS  "docs: ${bibletime-docs_SOURCE_DIR}" )


ExternalProject_Add(
    bibletime
    GIT_REPOSITORY https://github.com/jaakristioja/bibletime.git
    GIT_TAG e9496978f548e16b9baf02c9617031bb1381280b
    UPDATE_DISCONNECTED TRUE

    CONFIGURE_ENVIRONMENT_MODIFICATION
        "PKG_CONFIG_PATH=set:$ENV{PKG_CONFIG_PATH}"

    CMAKE_ARGS
        -DCMAKE_INSTALL_PREFIX=${PROJECT_INSTALL_DIR}
        -DCMAKE_PREFIX_PATH=${PROJECT_INSTALL_DIR}
        -DBUILD_HANDBOOK_HTML=OFF
        -DBUILD_HANDBOOK_PDF=OFF
        -DBUILD_HOWTO_HTML=OFF
        -DBUILD_HOWTO_PDF=OFF
        -DINSTALL_GENERATED_DOCS=ON
        -DCMAKE_BUILD_TYPE=Release
        -DGENERATED_DOCS_DIR=${bibletime-docs_SOURCE_DIR}/docs/${BT_DOC_REV}
        -DCMAKE_PREFIX_PATH=${Qt_PREFIX}
        -DQT_WINDEPLOYQT_EXECUTABLE=${Qt_PREFIX}/bin/windeployqt.exe
        -DMSVC_REDIST=${MSVC_REDIST}
        -DCMAKE_BUILD_TYPE=Release
        -DCMAKE_CONFIGURATION_TYPES=${CMAKE_CONFIGURATION_TYPES}
    INSTALL_COMMAND
        ${CMAKE_COMMAND} --build . --config Release --target install
    COMMAND
        ${CMAKE_COMMAND} --build . --config Release --target PACKAGE
    DEPENDS bibletime-docs
)
Link_Directories(${CMAKE_BINARY_DIR}/install/lib)

MESSAGE(STATUS  "Qt: ${Qt_PREFIX}" )
MESSAGE(STATUS  "Microsoft Redist: ${MSVC_REDIST}" )
