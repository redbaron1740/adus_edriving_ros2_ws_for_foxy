cmake_minimum_required(VERSION 3.5)
project(edrivingkit_pkg)

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

set(PCANBASIC_INCLUDE_DIRS /usr/include)
set(PCAN_LIBRARY /usr/lib/libpcanbasic.so)

include_directories(
  ${PROJECT_SOURCE_DIR}
  ${PROJECT_SOURCE_DIR}/src
  ${PCANBASIC_INCLUDE_DIRS}
  ${CMAKE_CURRENT_BINARY_DIR}/rosidl_generator_cpp   # 메시지 헤더가 생성되는 경로 추가
)

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(Threads REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/KitCmdMsgs.msg"
  "msg/KitFeedMsgs.msg"

 # DEPENDENCIES 

)


add_executable(edrivingKit_pcan_node src/eDrivingKit_pcan_node.cpp)
add_executable(edrivingKit_for_vcu_node src/eDrivingKit_for_vcu_node.cpp)
ament_target_dependencies(edrivingKit_pcan_node 
  rclcpp
  rosidl_default_runtime
)

add_dependencies(edrivingKit_pcan_node ${PROJECT_NAME}__cpp)
add_dependencies(edrivingKit_pcan_node ${PROJECT_NAME}__rosidl_typesupport_cpp)
add_dependencies(edrivingKit_for_vcu_node ${PROJECT_NAME}__cpp)
add_dependencies(edrivingKit_for_vcu_node ${PROJECT_NAME}__rosidl_typesupport_cpp)

target_link_libraries(edrivingKit_pcan_node
  ${PROJECT_NAME}__rosidl_typesupport_cpp
  ${PCAN_LIBRARY}
)

target_link_libraries(edrivingKit_for_vcu_node
  ${PROJECT_NAME}__rosidl_typesupport_cpp
  ${PCAN_LIBRARY}
)

ament_target_dependencies(edrivingKit_for_vcu_node
  rclcpp
  rosidl_default_runtime
)


install(TARGETS
  edrivingKit_pcan_node
  edrivingKit_for_vcu_node

  DESTINATION lib/${PROJECT_NAME}
)

# Install Python scripts
install(PROGRAMS
  src/Main_Prog_node.py
  DESTINATION lib/${PROJECT_NAME}
)


ament_export_dependencies(rosidl_default_runtime)

ament_package()
