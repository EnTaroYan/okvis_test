cmake_minimum_required(VERSION 2.8)

project(okvis_test)

set(OKVIS_INSTALLATION <path/to/install>) # point to installation

# EXTERNAL DEPENDENCIES
include(ExternalProject)
list(APPEND CMAKE_MODULE_PATH "/home/pi/okvis/cmake")
include_directories(${CMAKE_BINARY_DIR}/include)
link_directories(${CMAKE_BINARY_DIR}/lib)

# require Eigen
find_package( Eigen REQUIRED )
include_directories(${EIGEN_INCLUDE_DIR})

# require OpenCV
find_package( OpenCV COMPONENTS core highgui imgproc features2d REQUIRED )
include_directories(BEFORE ${OpenCV_INCLUDE_DIRS})

# require okvis
find_package( okvis 1.1 REQUIRED)
include_directories(${OKVIS_INCLUDE_DIRS})

# require brisk
find_package( brisk 2 REQUIRED)
include_directories(${BRISK_INCLUDE_DIRS})

# require ceres
list(APPEND CMAKE_PREFIX_PATH ${OKVIS_INSTALLATION})
find_package( Ceres REQUIRED )
include_directories(${CERES_INCLUDE_DIRS})

# require OpenGV
find_package(opengv REQUIRED)

# VISensor, if available
list(APPEND CMAKE_MODULE_PATH ${OKVIS_INSTALLATION}/lib/CMake)
find_package(VISensor)
if(VISENSORDRIVER_FOUND)
  message(STATUS "Found libvisensor.")
else()
  message(STATUS "libvisensor not found")
endif()

# glog
find_package(Glog REQUIRED)
include_directories(BEFORE ${GLOG_INCLUDE_DIRS})

add_executable(${PROJECT_NAME} "main.cpp")

target_link_libraries(${PROJECT_NAME}
    okvis_util
    okvis_kinematics
    okvis_time
    okvis_cv
    okvis_common
    okvis_ceres
    okvis_timing
    okvis_matcher
    okvis_frontend
    okvis_multisensor_processing
    pthread
    boost_system
    boost_filesystem
  )

if(${VISENSORDRIVER_FOUND})
    target_link_libraries(${PROJECT_NAME} ${VISensorDriver_LIBRARY})
endif()

