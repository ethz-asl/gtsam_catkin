gtsam_catkin
============

A catkin wrapper for GTSAM

This can be used one of two ways. 

1. Include this package in your workspace. It will download and compile the tarball of GTSAM.
2. Clone the gtsam source code (if you have access) into your workspace as `gtsam`. The source now has a basic `package.xml` file that will cause catkin to build it. This `gtsam_catkin` package then works as a convenicence bridge, automatically linking the gtsam libraries when the dependency is put in the `package.xml` of a downstream package.
