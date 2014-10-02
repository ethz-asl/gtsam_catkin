cmake_minimum_required(VERSION 2.8.3)
project(gtsam_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple(ALL_DEPS_REQUIRED)

# Make sure that Eigen is the patched GTSAM version.
find_package(eigen_catkin 0.0.301 EXACT)
if( NOT eigen_catkin_FOUND )

  message(FATAL_ERROR "--- GTSAM uses a patched version of Eigen. 
If you don't use this, you are going to have a bad time.
To get this version, simply go to your copy of eigen_catkin
and checkout the fix/gtsam_eigen branch.

> git checkout fix/gtsam_eigen

Then you should be good to go.")
endif()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(gtsam_src
  URL https://research.cc.gatech.edu/borg/sites/edu.borg/files/downloads/gtsam-3.1.0.tgz
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cmake -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX} 
                          -DCMAKE_BUILD_TYPE=Release ../gtsam_src
  BUILD_COMMAND make
  INSTALL_COMMAND make install
)

add_dependencies(${PROJECT_NAME}_package gtsam_src)

cs_install()
cs_export(CFG_EXTRAS gtsam-extras.cmake)


