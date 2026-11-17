cmake_minimum_required(VERSION 3.10.0)

if(POLICY CMP0000)
    cmake_policy(SET CMP0000 NEW)
endif()

project(OTIS_Task02 LANGUAGES CXX)

enable_testing()
add_subdirectory(src)