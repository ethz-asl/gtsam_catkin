cmake_minimum_required(VERSION 3.5)
project(gtsam_vendor)

find_package(ament_cmake REQUIRED)
find_package(ament_cmake_python REQUIRED)
ament_add_default_options()

include(ExternalProject)

externalproject_add(gtsam
  GIT_REPOSITORY https://github.com/borglab/gtsam.git
  GIT_TAG 005c7d4e2debcfe72c89ac78ad68683ea4e71d5d
  GIT_CONFIG advice.detachedHead=false
  UPDATE_COMMAND ""
  CMAKE_ARGS  -DGTSAM_BUILD_PYTHON=ON
              -DGTSAM_USE_SYSTEM_EIGEN=ON
              -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install
)

externalproject_get_property(gtsam BINARY_DIR)

ament_python_install_package(gtsam)
ament_export_include_directories(include)
ament_export_libraries(gtsam gtsam_unstable)

install(
  DIRECTORY
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install/
  DESTINATION
    ${CMAKE_INSTALL_PREFIX}
  USE_SOURCE_PERMISSIONS
)

install(
  DIRECTORY
    ${BINARY_DIR}/python/gtsam ${BINARY_DIR}/python/gtsam_unstable
  DESTINATION
    ${PYTHON_INSTALL_DIR}
  USE_SOURCE_PERMISSIONS
)

# Testing
if(BUILD_TESTING)
  # Test linting
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()

endif()

ament_package()