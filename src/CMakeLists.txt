cmake_minimum_required(VERSION 2.8.11)

project(core)

set(QT_MIN_VERSION "5.6.0")
find_package(Qt5 ${QT_MIN_VERSION} CONFIG REQUIRED
    Core
    Qml
    )

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

file(GLOB_RECURSE DATABASE_SRCS
    RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/*.h
    )
add_library(core SHARED ${DATABASE_SRCS})
set_target_properties(core PROPERTIES CXX_STANDARD 14)
target_compile_definitions(core PUBLIC CORE_LIBRARY)
# Doesn't work because it includes the project prefix for some reason...
#target_include_directories(core PUBLIC
#    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR} ${Qt5Qml_PRIVATE_INCLUDE_DIRS}>
#    $<INSTALL_INTERFACE:include>
#    )
set_target_properties(core PROPERTIES
    INCLUDE_DIRECTORIES "${Qt5Qml_PRIVATE_INCLUDE_DIRS}"
    INTERFACE_INCLUDE_DIRECTORIES "${Qt5Qml_PRIVATE_INCLUDE_DIRS}"
    )
target_include_directories(core PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include>
    )
target_link_libraries(core
    Qt5::Core
    Qt5::Qml
    $<$<BOOL:${WIN32}>:Pdh>
    )

set(CORE_INSTALL_PATH "plugins/QFlow/Core" CACHE PATH "qFlow Core Library Install Path")

install(TARGETS core
    EXPORT coreConfig
    RUNTIME DESTINATION ${CORE_INSTALL_PATH}
    ARCHIVE DESTINATION lib
    COMPONENT qflow
    )
install(EXPORT coreConfig DESTINATION lib/cmake)
install(FILES qmldir plugins.qmltypes DESTINATION ${CORE_INSTALL_PATH} COMPONENT qflow)
