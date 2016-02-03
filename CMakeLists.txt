cmake_minimum_required(VERSION 2.8.3)
project(gtsam_catkin)

find_package(catkin_simple REQUIRED)

# Don't require all deps. This will look for the source
# version of gtsam first, then download the public version
# if it is not found.
catkin_simple()

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


# Check if the source version of GTSAM is available
find_package(gtsam NO_MODULE CONFIGS gtsamConfig.cmake GTSAMConfig.cmake)
if(gtsam_FOUND)
  message("+++ Found source version of GTSAM (using ${GTSAM_INCLUDE_DIR})")
  add_dependencies(${PROJECT_NAME}_package gtsam)
else()
  message("--- No source version of GTSAM found. Downloading.")
  include(ExternalProject)
  file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)
  ExternalProject_Add(gtsam_src
    URL https://research.cc.gatech.edu/borg/sites/edu.borg/files/downloads/gtsam-3.2.1.tgz
    UPDATE_COMMAND ""
	PATCH_COMMAND cd ../gtsam_src/ && patch -N gtsam/nonlinear/LinearContainerFactor.h ${CMAKE_CURRENT_SOURCE_DIR}/LinearContainerFactor.patch
    CONFIGURE_COMMAND cmake -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX} 
    -DCMAKE_BUILD_TYPE=Release ../gtsam_src
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND make install
    )
  ExternalProject_CatkinInstall(gtsam_src)
  add_dependencies(${PROJECT_NAME}_package gtsam_src)
endif()

cs_install()
cs_export(CFG_EXTRAS gtsam-extras.cmake)


