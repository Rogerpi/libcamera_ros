cmake_minimum_required(VERSION 3.5)
project(libcamera_ros)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# force the linker to link libcamera_source even when it is not needed for the wrapper lib
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,-no-as-needed")

# set default build type
# https://blog.kitware.com/cmake-and-the-default-build-type/
set(CATKIN_DEPENDENCIES
  cmake_modules
  roscpp
)

find_package(PkgConfig REQUIRED)

pkg_check_modules(LIBCAMERA REQUIRED libcamera)
message(STATUS "libcamera library found:")
message(STATUS "    version: ${LIBCAMERA_VERSION}")
message(STATUS "    libraries: ${LIBCAMERA_LINK_LIBRARIES}")
message(STATUS "    include path: ${LIBCAMERA_INCLUDE_DIRS}")

find_package(catkin REQUIRED COMPONENTS
  ${CATKIN_DEPENDENCIES}
)

set(LIBRARIES
  LibCameraRos_Wrapper
)

catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS ${CATKIN_DEPENDENCIES}
  LIBRARIES ${LIBRARIES}
  DEPENDS
  LIBCAMERA
)

include_directories(
  include
)

add_library(LibCameraRos_Wrapper
  src/libcamera_ros_wrapper.cpp
)

target_include_directories(LibCameraRos_Wrapper
  PUBLIC
  include
  ${LIBCAMERA_INCLUDE_DIRS}
)

target_link_libraries(LibCameraRos_Wrapper
  ${LIBCAMERA_LINK_LIBRARIES}
)

# # --------------------------------------------------------------
# # |                           Install                          |
# # --------------------------------------------------------------
install(TARGETS ${LIBRARIES}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

install(DIRECTORY include/libcamera_ros/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
