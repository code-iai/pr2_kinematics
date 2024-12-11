cmake_minimum_required(VERSION 3.10)
project(pr2_arm_kinematics)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_action REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(angles REQUIRED)
find_package(urdf REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(kdl_parser REQUIRED)
find_package(moveit_msgs REQUIRED)
find_package(moveit_core REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tinyxml REQUIRED)
#find_package(moveit_srvs REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)
include_directories(pr2_arm_kinematics/include)
add_executable(pr2_arm_kinematics_node src/main.cpp)
target_include_directories(pr2_arm_kinematics_node PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}/src>)
target_compile_features(pr2_arm_kinematics_node PUBLIC c_std_99 cxx_std_17)  # Require C99 and C++17

ament_target_dependencies(pr2_arm_kinematics_node
    rclcpp
    tf2_ros
    angles
    urdf
    kdl_parser
    moveit_msgs
    moveit_core
    std_msgs
    geometry_msgs
    tinyxml
    )

add_library(pr2_arm_kinematics_lib
  src/pr2_arm_ik.cpp
  src/pr2_arm_ik_solver.cpp
  src/pr2_arm_kinematics_utils.cpp
  src/pr2_arm_kinematics.cpp
  src/pr2_arm_kinematics_plugin.cpp
)

ament_target_dependencies(pr2_arm_kinematics_lib
    rclcpp
    tf2_ros
    angles
    urdf
    kdl_parser
    moveit_msgs
    moveit_core
    std_msgs
    tinyxml
    )


target_include_directories(pr2_arm_kinematics_lib PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}/src>)
target_link_libraries(pr2_arm_kinematics_lib tinyxml2::tinyxml2)

install(TARGETS pr2_arm_kinematics_node
  DESTINATION lib/${PROJECT_NAME})

target_link_libraries(pr2_arm_kinematics_node Eigen3::Eigen)
target_link_libraries(pr2_arm_kinematics_node pr2_arm_kinematics_lib)


if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
