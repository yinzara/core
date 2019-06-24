cmake_minimum_required(VERSION 2.8.11)

project(core)

set(PACKAGE_NAME "QFlowCore")

set(QT_MIN_VERSION "5.6.0")
find_package(Qt5 ${QT_MIN_VERSION} CONFIG REQUIRED
    Core
    Qml
    )

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

file(GLOB_RECURSE CORE_SOURCES *.cpp)
file(GLOB_RECURSE CORE_HEADERS *.h)
add_library(core SHARED ${CORE_SOURCES})
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
    $<INSTALL_INTERFACE:include/${PACKAGE_NAME}>
    )
target_link_libraries(core
    Qt5::Core
    Qt5::Qml
    $<$<BOOL:${WIN32}>:Pdh>
    )

set(CORE_INSTALL_PATH "modules/QFlow/Core" CACHE PATH "qFlow Core Library Install Path")

install(TARGETS core
    EXPORT ${PACKAGE_NAME}Config
    RUNTIME DESTINATION ${CORE_INSTALL_PATH}
    ARCHIVE DESTINATION lib
    COMPONENT qflow
    )
install(FILES ${CORE_HEADERS} DESTINATION include/${PACKAGE_NAME} COMPONENT qflow)
install(EXPORT ${PACKAGE_NAME}Config
    NAMESPACE QFlow::
    DESTINATION lib/cmake/${PACKAGE_NAME}
    )
install(FILES qmldir plugins.qmltypes DESTINATION ${CORE_INSTALL_PATH} COMPONENT qflow)
