cmake_minimum_required(VERSION 3.18 FATAL_ERROR)

project(fcl_test)

if(WIN32)
    set(FCL_INCLUDE_DIRS 
        C:/Users/msagardia/play/git_repositories/foreign/fcl/build/include
        C:/Users/msagardia/play/git_repositories/foreign/fcl/include
        )
    set(FCL_LIBRARY_DIRS C:/Users/msagardia/play/git_repositories/foreign/fcl/build/lib/Release)
    set(FCL_LIBRARIES fcl)
    set(EIGEN_INCLUDE_DIR C:/src/Eigen3/eigen)
    set(CCD_INCLUDE_DIR "C:/Program Files (x86)/libccd/include")
elseif(APPLE)
    set(FCL_INCLUDE_DIRS /usr/local/include)
    set(FCL_LIBRARY_DIRS /usr/local/lib)
    set(FCL_LIBRARIES fcl)
    set(EIGEN_INCLUDE_DIR /usr/local/include/eigen3)
    set(CCD_INCLUDE_DIR /usr/local/include)
elseif(LINUX)
endif(WIN32)

# Eigen
include_directories(${EIGEN_INCLUDE_DIR})

# Octomap
find_package(octomap REQUIRED)
include_directories(${OCTOMAP_INCLUDE_DIRS})
link_libraries(${OCTOMAP_LIBRARIES})

# FCL
include_directories(${FCL_INCLUDE_DIRS})
link_directories(${FCL_LIBRARY_DIRS})

# CCD
include_directories(${CCD_INCLUDE_DIR})

# Compilation -> add_executable / add_library
set (CMAKE_CXX_FLAGS "-std=c++14 -O0 -pedantic -Wall -Wfatal-errors -Wextra")
# Linking -> target_link_libraries
set (LDFLAGS "-std=c++14")

add_executable (fcl_test main.cpp)
target_link_libraries (fcl_test ${FCL_LIBRARIES})
