cmake_minimum_required(VERSION 2.8.12)

project(precise_driver)

## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++11)

# use clang compiler
set(CMAKE_CXX_COMPILER  "/usr/bin/clang++" )
set(CMAKE_C_COMPILER    "/usr/bin/clang"   )

find_package(catkin REQUIRED COMPONENTS industrial_robot_client simple_message)

catkin_package(
  CATKIN_DEPENDS industrial_robot_client
)

# The definition is copied from the CMakeList for the simple_message package.
add_definitions(-DROS=1)           #build using ROS libraries
add_definitions(-DLINUXSOCKETS=1)  #build using LINUX SOCKETS libraries

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

add_executable(precise_robot_state
  src/robot_state_node.cpp)
target_link_libraries(precise_robot_state 
  industrial_robot_client
  simple_message
  ${catkin_LIBRARIES})

add_executable(precise_motion_streaming_interface
  src/joint_streamer_node.cpp)
target_link_libraries(precise_motion_streaming_interface 
  industrial_robot_client 
  simple_message
  ${catkin_LIBRARIES})

add_executable(precise_motion_download_interface
  src/joint_downloader_node.cpp)
target_link_libraries(precise_motion_download_interface 
  industrial_robot_client 
  simple_message
  ${catkin_LIBRARIES})

add_executable(precise_joint_trajectory_action 
  src/joint_trajectory_action_node.cpp
  src/joint_trajectory_action.cpp)
target_link_libraries(precise_joint_trajectory_action 
  industrial_robot_client ${catkin_LIBRARIES})

install(TARGETS 
        precise_robot_state 
        precise_motion_streaming_interface 
        precise_motion_download_interface 
        precise_joint_trajectory_action 
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(
    DIRECTORY include/${PROJECT_NAME}/
    DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

foreach(dir config launch)
   install(DIRECTORY ${dir}/
      DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/${dir})
endforeach(dir)
