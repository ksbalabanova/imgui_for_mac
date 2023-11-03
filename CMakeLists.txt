cmake_minimum_required(VERSION 3.26)
project(kettle)

add_executable(kettle main.cpp)
target_compile_features(kettle PUBLIC cxx_std_20)
