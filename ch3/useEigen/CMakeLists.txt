cmake_minimum_required(VERSION 2.8)
project(useEigen)

#set(CMAKE_BUILD_TYPE "Release")        # using it on the cmd line explicitly
#set(CMAKE_CXX_FLAGS "-O3")
set(CMAKE_CXX_FLAGS "-O2")              # -O2 is good!

# 添加Eigen头文件
include_directories("/usr/include/eigen3")
add_executable(eigenMatrix eigenMatrix.cpp)
