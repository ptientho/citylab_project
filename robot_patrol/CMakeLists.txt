# include/robot_patrol/direction_service.hpp #

cmake_minimum_required(VERSION 3.5)
project(robot_patrol)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_action REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(robot_patrol_interface REQUIRED)
find_package(tf2 REQUIRED)
find_package(nav_msgs REQUIRED)

# for user libraries
include_directories(include)

# define the library as target
add_library(direction_service_lib SHARED src/direction_service.cpp)
ament_target_dependencies(direction_service_lib rclcpp geometry_msgs sensor_msgs robot_patrol_interface)
ament_export_libraries(direction_service_lib)
add_executable(direction_service_node src/direction_service.cpp)
ament_target_dependencies(direction_service_node rclcpp geometry_msgs sensor_msgs robot_patrol_interface)

# create executable target and link it with library
add_executable(patrol_node src/patrol.cpp)
ament_target_dependencies(patrol_node rclcpp geometry_msgs sensor_msgs)
target_link_libraries(patrol_node direction_service_lib)


add_executable(test_service_node src/test_service.cpp)
ament_target_dependencies(test_service_node rclcpp sensor_msgs robot_patrol_interface)

add_executable(patrol_v2_node src/patrol_v2.cpp)
ament_target_dependencies(patrol_v2_node rclcpp sensor_msgs robot_patrol_interface)

add_executable(go_to_point_action_node src/go_to_point_action.cpp)
ament_target_dependencies(go_to_point_action_node rclcpp rclcpp_action geometry_msgs nav_msgs robot_patrol_interface tf2)


#add_executable(direction_service_node src/direction_service.cpp)
#ament_target_dependencies(direction_service_node rclcpp geometry_msgs sensor_msgs)
#target_link_libraries(direction_service_node direction_service_lib)

# install target executables in the lib folder to be seen by setup.bash
install(TARGETS
  patrol_node
  direction_service_node
  test_service_node
  patrol_v2_node
  go_to_point_action_node
  DESTINATION lib/${PROJECT_NAME}/
)

# install include directories which has headers to be exported
install(DIRECTORY
  include/
  DESTINATION include
)

# install launch files
install(DIRECTORY
  launch
  DESTINATION share/${PROJECT_NAME}
)

install(
  TARGETS direction_service_lib
  EXPORT direction_service_lib
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
)


if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()
 


ament_package()