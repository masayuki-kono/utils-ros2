cmake_minimum_required(VERSION 3.5)
project(aws_ros2_common)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(aws_common REQUIRED)

include_directories(include
    ${rclcpp_INCLUDE_DIRS}
    ${aws_common_INCLUDE_DIRS})
add_library(${PROJECT_NAME} SHARED
    src/sdk_utils/ros2_node_parameter_reader.cpp
    src/sdk_utils/logging/aws_ros_logger.cpp)

ament_target_dependencies(${PROJECT_NAME} rclcpp aws_common)

install(TARGETS ${PROJECT_NAME}
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

install(
    DIRECTORY include/
    DESTINATION include/
)

if(BUILD_TESTING)
  find_package(ament_cmake_gtest REQUIRED)
  
  ament_add_gtest(test_configuration_provider test/client_configuration_provider_test.cpp)
  target_link_libraries(test_configuration_provider
      ${aws_common_LIBRARIES}
      ${PROJECT_NAME}
      ${rclcpp_LIBRARIES}
  )

  ament_add_gtest(test_logger test/sdk_utils/logging/aws_ros_logger_test.cpp)
  target_link_libraries(test_logger
      ${aws_common_LIBRARIES}
      ${PROJECT_NAME}
      ${rclcpp_LIBRARIES}
  )

  ament_add_gtest(test_parameter_reader test/parameter_reader_test.cpp)
  target_link_libraries(test_parameter_reader
      ${aws_common_LIBRARIES}
      ${PROJECT_NAME}
      ${rclcpp_LIBRARIES}
  )
endif()

ament_export_libraries(${PROJECT_NAME})
ament_export_include_directories("include")
ament_package()
