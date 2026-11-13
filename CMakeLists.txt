cmake_minimum_required(VERSION 3.20)
project(CESengine VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

option(CES_BUILD_TESTS "Build CESengine CI verification executable" ${PROJECT_IS_TOP_LEVEL})

find_package(SFML 3.0 REQUIRED COMPONENTS Graphics Window System)

add_library(CESengine INTERFACE)
add_library(CES::Engine ALIAS CESengine)

target_compile_features(CESengine INTERFACE cxx_std_20)
target_include_directories(CESengine INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
target_link_libraries(CESengine INTERFACE
    SFML::Graphics
    SFML::Window
    SFML::System
)

if(CES_BUILD_TESTS)
    enable_testing()
    set(CI_SMOKE_TEST "${CMAKE_CURRENT_BINARY_DIR}/ci_smoke_test.cpp")
    file(WRITE "${CI_SMOKE_TEST}" "#include <include/ces.h>\nint main() { return 0; }\n")
    add_executable(ces_ci_test "${CI_SMOKE_TEST}")
    target_link_libraries(ces_ci_test PRIVATE CES::Engine)
    add_test(NAME HeaderCompileCheck COMMAND ces_ci_test)
endif()
