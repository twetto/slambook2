cmake_minimum_required(VERSION 2.8)
project(dense_monocular)

set(CMAKE_BUILD_TYPE "Release")
#set(CMAKE_CXX_FLAGS "-std=c++11 -march=native -O3")
set(CMAKE_CXX_FLAGS "-march=native -O2")
set(CMAKE_CXX_STANDARD 14)

############### dependencies ######################
# Eigen
include_directories("/usr/include/eigen3")
# OpenCV
#find_package(OpenCV 3.1 REQUIRED)
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})
# Sophus
find_package(fmt REQUIRED)
set(FMT_LIBRARIES fmt::fmt)
find_package(Sophus REQUIRED)
include_directories(${Sophus_INCLUDE_DIRS})
# OpenMP
set(OPENMP_LIBRARIES OpenMP::OpenMP_CXX)
find_package(OpenMP REQUIRED)

set(THIRD_PARTY_LIBS
        ${OpenCV_LIBS}
        ${Sophus_LIBRARIES}
        ${FMT_LIBRARIES}
        ${OPENMP_LIBRARIES})

add_executable(dense_mapping dense_mapping.cpp)
target_link_libraries(dense_mapping ${THIRD_PARTY_LIBS})
