cmake_minimum_required(VERSION 3.15)

if(POLICY CMP0135)
    cmake_policy(SET CMP0135 NEW)
endif()

project(OTIS_Task02 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

enable_testing()

include(FetchContent)
FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/refs/tags/v1.14.0.zip
    DOWNLOAD_EXTRACT_TIMESTAMP TRUE
)
set(CMAKE_POLICY_VERSION_MINIMUM 3.5 CACHE STRING "" FORCE)
FetchContent_MakeAvailable(googletest)

# Ядро логики: модели и сигналы из task_01 + новые модули task_02
add_library(otis_core
    ../task_01/src/Model1_9.cpp
    ../task_01/src/Model2_6.cpp
    ../task_01/src/Model3_9.cpp
    ../task_01/src/InputSignal.cpp
    src/StabilityChecker.cpp
    src/Simulator.cpp
)

target_include_directories(otis_core
    PUBLIC
        src
        ../task_01/src
)

# Исполняемый файл симуляции
add_executable(otis_app src/main.cpp)
target_link_libraries(otis_app PRIVATE otis_core)

# Исполняемый файл модульных тестов
add_executable(otis_tests
    tests/test_models.cpp
    tests/test_stability.cpp
    tests/test_simulator.cpp
)

target_link_libraries(otis_tests
    PRIVATE
        otis_core
        gtest
        gtest_main
        gmock
)

# Флаги покрытия для GCC / MinGW
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(otis_core PRIVATE --coverage -O0 -g)
    target_compile_options(otis_tests PRIVATE --coverage -O0 -g)
    target_link_options(otis_app PRIVATE --coverage)
    target_link_options(otis_tests PRIVATE --coverage)
endif()

include(GoogleTest)
gtest_discover_tests(otis_tests)