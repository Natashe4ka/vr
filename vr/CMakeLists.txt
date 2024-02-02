cmake_minimum_required(VERSION 3.22)
project(vr)

find_package(ament_cmake REQUIRED)


if(CMAKE_CXX_COMPILER_ID MATCHES "(GNU|Clang)")
  add_compile_options(-Wall -Wextra)
endif()

set(PACKAGE_INCLUDE_DEPENDS
  hardware_interface
  pluginlib
  rclcpp
  rclcpp_lifecycle
)

find_package(ament_cmake REQUIRED)
foreach(Dependency IN ITEMS ${PACKAGE_INCLUDE_DEPENDS})
  find_package(${Dependency} REQUIRED)
endforeach()

add_library(
  vr
  SHARED
  hardware/vr_hardware.cpp
 
)

target_compile_features(vr PUBLIC cxx_std_17)
target_include_directories(vr PUBLIC
$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/hardware/include>

$<INSTALL_INTERFACE:include/vr>
)
ament_target_dependencies(
  vr PUBLIC
  ${PACKAGE_INCLUDE_DEPENDS}
 
)

target_compile_definitions(${PROJECT_NAME} PRIVATE "VR_BUILDING_DLL")

pluginlib_export_plugin_description_file(hardware_interface vr.xml)

install(DIRECTORY launch DESTINATION share/${PROJECT_NAME}
  PATTERN "setup_assistant.launch" EXCLUDE)
install(DIRECTORY config DESTINATION share/${PROJECT_NAME})
install(FILES .setup_assistant DESTINATION share/${PROJECT_NAME})


install(
  DIRECTORY hardware/include/
  DESTINATION include/vr
)



install(TARGETS vr
  EXPORT export_vr
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

## EXPORTS
ament_export_targets(export_vr HAS_LIBRARY_TARGET)
ament_export_dependencies(${PACKAGE_INCLUDE_DEPENDS})




ament_package()
