cmake_minimum_required(VERSION 2.8.3)
project(gtsam_catkin)

find_package(catkin_simple REQUIRED)

catkin_simple()

include(ExternalProject)
file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)
ExternalProject_Add(gtsam_src
  GIT_REPOSITORY https://bitbucket.org/gtborg/gtsam.git
  GIT_TAG 687ae3d2511b9c296af08ec2f2e717b0627a8d68
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cmake -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX} -DGTSAM_USE_SYSTEM_EIGEN=ON
  -DCMAKE_BUILD_TYPE=Release ../gtsam_src
  BUILD_COMMAND make
  INSTALL_COMMAND make install
)

cs_install()
cs_export(CFG_EXTRAS gtsam-extras.cmake)
