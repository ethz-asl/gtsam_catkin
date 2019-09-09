cmake_minimum_required(VERSION 2.8.3)
project(gtsam_catkin)

find_package(catkin_simple REQUIRED)

catkin_simple()

include(ExternalProject)
file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)
ExternalProject_Add(gtsam_src
  GIT_REPOSITORY https://github.com/borglab/gtsam.git
  GIT_TAG 438b4ff014447c59ae1fa2b9d4115e348a7df36b
  UPDATE_COMMAND ""
  PATCH_COMMAND patch -p1 < ${CMAKE_CURRENT_SOURCE_DIR}/use_catkinized_metis.patch &&
  patch -p1 < ${CMAKE_CURRENT_SOURCE_DIR}/fix_warnings.patch
  CONFIGURE_COMMAND cmake -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX} -DGTSAM_USE_SYSTEM_EIGEN=ON
  -DCMAKE_BUILD_TYPE=Release ../gtsam_src
  BUILD_COMMAND make -j2
  INSTALL_COMMAND make install
)

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/CppUnitLite
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/gtsam
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/gtsam_unstable
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/wrap
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        FILES_MATCHING PATTERN "libCppUnitLite*")
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        FILES_MATCHING PATTERN "libgtsam*")

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          LIBRARIES CppUnitLite gtsam gtsam_unstable metis tbb)
