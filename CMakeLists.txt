cmake_minimum_required(VERSION 2.8.3)
project(youbot_arm_kinematics)

add_compile_options(-std=c++11 -I /usr/include/eigen3/)

find_package(catkin REQUIRED COMPONENTS
    roscpp
    rospy
    std_msgs
    message_generation
    sensor_msgs
    tf
)

catkin_python_setup()

add_service_files(FILES IK.srv)
generate_messages(DEPENDENCIES std_msgs)

catkin_package(
    INCLUDE_DIRS include 
    LIBRARIES libkinematics
    CATKIN_DEPENDS message_runtime
)

include_directories(include include/youbot_arm_kinematics ${catkin_INCLUDE_DIRS})

catkin_install_python(PROGRAMS scripts/fk_test scripts/ik_test
                      DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

add_library(libkinematics src/kinematics.cpp src/jacobi.cpp)
target_link_libraries(libkinematics  ${catkin_LIBRARIES})

add_executable(fk_test_node src/fk_test_node.cpp)
target_link_libraries(fk_test_node libkinematics ${catkin_LIBRARIES})
add_dependencies(fk_test_node ${catkin_EXPORTED_TARGETS})

add_executable(ik_test_node src/ik_test_node.cpp)
target_link_libraries(ik_test_node libkinematics ${catkin_LIBRARIES})
add_dependencies(ik_test_node ${catkin_EXPORTED_TARGETS})

install(TARGETS libkinematics
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
