cmake_minimum_required(VERSION 2.8.3)
project(behavior_tree_core)



find_package(catkin REQUIRED COMPONENTS
  actionlib
  actionlib_msgs
  message_generation
  roscpp
  rospy
  std_msgs
  roslaunch
  genmsg
)


add_action_files(
  DIRECTORY action
  FILES BT.action
)


generate_messages(
  DEPENDENCIES actionlib_msgs std_msgs # Or other packages containing msgs
)


add_definitions(-Wall -lglut -lGL -std=c++0x)

catkin_package(
  CATKIN_DEPENDS actionlib_msgs
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
)



#########################################################
# FIND GLUT
#########################################################
find_package(GLUT REQUIRED)
include_directories(${GLUT_INCLUDE_DIRS})
link_directories(${GLUT_LIBRARY_DIRS})
add_definitions(${GLUT_DEFINITIONS})
if(NOT GLUT_FOUND)
    message(ERROR " GLUT not found!")
endif(NOT GLUT_FOUND)

#########################################################
# FIND OPENGL
#########################################################
find_package(OpenGL REQUIRED)
include_directories(${OpenGL_INCLUDE_DIRS})
link_directories(${OpenGL_LIBRARY_DIRS})
add_definitions(${OpenGL_DEFINITIONS})
if(NOT OPENGL_FOUND)
    message(ERROR " OPENGL not found!")
endif(NOT OPENGL_FOUND)

#########################################################
# FIND GTest
#########################################################
find_package(GTest)
include_directories(${GTEST_INCLUDE_DIRS})


INCLUDE_DIRECTORIES(${catkin_INCLUDE_DIRS} include)

file(GLOB_RECURSE BTHeadLibrary include/*.h)

set(BTSrcLibrary
src/action_node.cpp
src/behavior_tree.cpp
src/condition_node.cpp
src/control_node.cpp
src/decorators/negation_node.cpp
#src/decorator_retry_node.cpp
#src/decorator_negation_node.cpp
src/draw.cpp
src/exceptions.cpp
src/leaf_node.cpp
src/tick_engine.cpp
src/parallel_node.cpp
src/fallback_node.cpp
src/sequence_node.cpp
src/decorator_node.cpp
src/fallback_node_with_memory.cpp
src/sequence_node_with_memory.cpp
src/tree_node.cpp
src/actions/action_test_node.cpp
src/conditions/condition_test_node.cpp
src/actions/ros_action.cpp
src/conditions/ros_condition.cpp
src/dot_bt.cpp
)

# Compile the core library with name ${PROJECT_NAME}=behavior_tree_core
# You can create executables which target to this library for using BTs
add_library(${PROJECT_NAME} ${BTSrcLibrary} ${BTHeadLibrary})
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES} ${OPENGL_LIBRARIES} ${GLUT_LIBRARY})
add_dependencies(${PROJECT_NAME} behavior_tree_core_generate_messages_cpp)

add_executable(tree src/tree.cpp)
target_link_libraries(tree
  ${catkin_LIBRARIES}
  ${PROJECT_NAME})

add_executable(gtest_tree src/gtest/gtest_tree.cpp)
target_link_libraries(gtest_tree
  ${catkin_LIBRARIES}
  ${PROJECT_NAME}
  ${GTEST_LIBRARIES})

add_executable(gtest_ros src/gtest/external_ros_nodes_test.cpp)
target_link_libraries(gtest_ros
  ${catkin_LIBRARIES}
  ${PROJECT_NAME}
  ${GTEST_LIBRARIES})


 #-----------------------------------# 
#DEMO-1 COMPILITION
add_executable(demo1 src/rehab_robot_demo/demo1.cpp)
target_link_libraries(demo1
  ${catkin_LIBRARIES}
  ${PROJECT_NAME}
  ${GTEST_LIBRARIES})

#DEMO-2 COMPILITION
add_executable(demo2 src/rehab_robot_demo/demo2.cpp)
target_link_libraries(demo2
  ${catkin_LIBRARIES}
  ${PROJECT_NAME}
  ${GTEST_LIBRARIES})

#DEMO-2 Method-2 COMPILITION 
add_executable(demo2_method2 src/rehab_robot_demo/demo2_method2.cpp)
target_link_libraries(demo2_method2
  ${catkin_LIBRARIES}
  ${PROJECT_NAME}
  ${GTEST_LIBRARIES})

#add_executable(ros_test src/ros_test.cpp ${BTSrcLibrary} ${BTHeadLibrary})
#target_link_libraries(ros_test ${catkin_LIBRARIES} ${OPENGL_LIBRARIES} ${GLUT_LIBRARY})
#add_dependencies(ros_test behavior_tree_core_generate_messages_cpp)


