cmake_minimum_required(VERSION 3.12)
project(elevation_mapping_cupy)

find_package(PythonInterp 3 REQUIRED)
find_package(PythonLibs 3 REQUIRED)
find_package(OpenCV REQUIRED)

if(PYTHONLIBS_FOUND)
  message(STATUS "Using Python Libraries at: " ${PYTHON_LIBRARIES})
  message(STATUS "Using Python include directories at: " ${PYTHON_INCLUDE_DIRS})
else()
  message(WARNING "Could not find Python Libraries")
endif()

find_package(Eigen3 REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclpy REQUIRED)
find_package(tf2 REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(std_msgs REQUIRED)
find_package(std_srvs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(grid_map_ros REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(grid_map_msgs REQUIRED)
find_package(image_transport REQUIRED)
find_package(pybind11_vendor REQUIRED)
find_package(elevation_map_msgs REQUIRED)

set(dependencies
    rclcpp
    rclpy
    tf2
    pcl_ros
    std_msgs
    std_srvs
    sensor_msgs
    grid_map_ros
    geometry_msgs
    grid_map_msgs
    image_transport
    pybind11_vendor
    elevation_map_msgs
)

include_directories(
  include
  ${PYTHON_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
)

add_library(elevation_mapping_ros
    src/elevation_mapping_wrapper.cpp
    src/elevation_mapping_ros.cpp)

ament_target_dependencies(elevation_mapping_ros ${dependencies})
target_link_libraries(elevation_mapping_ros ${PYTHON_LIBRARIES} ${OpenCV_LIBRARIES})

add_executable(elevation_mapping_node src/elevation_mapping_node.cpp)
ament_target_dependencies(elevation_mapping_node ${dependencies})
target_link_libraries(elevation_mapping_node elevation_mapping_ros)

# pybind11_vendor()

install(
  TARGETS elevation_mapping_ros elevation_mapping_node
  EXPORT export_${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)

install(
  DIRECTORY launch config
  DESTINATION share/${PROJECT_NAME})

install(DIRECTORY include/ DESTINATION include/${PROJECT_NAME})
  

ament_export_dependencies(${dependencies})  
ament_export_include_directories("include/${PROJECT_NAME}")
ament_export_targets(export_${PROJECT_NAME} HAS_LIBRARY_TARGET)
ament_package()