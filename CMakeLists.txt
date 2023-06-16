cmake_minimum_required(VERSION 3.1)
project(DRAMSim2
    VERSION 2.2.2
    LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)

set(SRC
    AddressMapping.cpp
    Bank.cpp
    BankState.cpp
    BusPacket.cpp
    ClockDomain.cpp
    CommandQueue.cpp
    IniReader.cpp
    MemoryController.cpp
    MemorySystem.cpp
    MultiChannelMemorySystem.cpp
    Rank.cpp
    SimulatorObject.cpp
    TraceBasedSim.cpp
    Transaction.cpp
)

set(INSTALLED_HEADERS
    Callback.h
    DRAMSim.h
    SimulatorObject.h
)

if(BUILD_EXE)
    set(_TARGET_NAME DRAMSim)
    add_executable(${_TARGET_NAME} ${SRC})
    target_compile_definitions(${_TARGET_NAME} PUBLIC NO_STORAGE)
    install(TARGETS ${_TARGET_NAME})
    unset(_TARGET_NAME)
elseif(BUILD_LIB)
    set(_TARGET_NAME dramsim)
    add_library(${_TARGET_NAME} ${SRC})
    target_compile_definitions(${_TARGET_NAME} PUBLIC NO_STORAGE)
    target_include_directories(${_TARGET_NAME} PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>)
    set_target_properties(${_TARGET_NAME} PROPERTIES PUBLIC_HEADER "${INSTALLED_HEADERS}")
    install(TARGETS ${_TARGET_NAME} EXPORT ${_TARGET_NAME}-targets)
    set(_CMAKE_INSTALL_DIR share/${CMAKE_PROJECT_NAME}/cmake)
    install(EXPORT ${_TARGET_NAME}-targets
        NAMESPACE ${CMAKE_PROJECT_NAME}::
        DESTINATION ${_CMAKE_INSTALL_DIR})
    include(CMakePackageConfigHelpers)
    configure_package_config_file(
        ${CMAKE_PROJECT_NAME}Config.cmake.in
        ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}Config.cmake
        INSTALL_DESTINATION ${_CMAKE_INSTALL_DIR}
    )
    write_basic_package_version_file(
        ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}ConfigVersion.cmake
        COMPATIBILITY AnyNewerVersion
    )
    install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}Config.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}ConfigVersion.cmake
        DESTINATION ${_CMAKE_INSTALL_DIR})
    unset(_CMAKE_INSTALL_DIR)
    unset(_TARGET_NAME)
endif()
