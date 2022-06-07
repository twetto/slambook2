cmake_minimum_required(VERSION 2.8)
project(chapter3)

#set(CMAKE_CXX_FLAGS "-std=c++11")  # 這行在我機器上不能用啦
set(CMAKE_CXX_STANDARD 11)          # 這行才可以

# 加入各子工程
add_subdirectory(useEigen)
add_subdirectory(useGeometry)
add_subdirectory(visualizeGeometry)
add_subdirectory(examples)
