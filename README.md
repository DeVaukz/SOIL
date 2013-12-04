# libSOIL with CMake
This is libSOIL (Simple OpenGL Image Library) modified to use the CMake build system.  Care has been taken to make everything build and install nicely on OS X.  By default, libSOIL.a and libSOIL.dylib will be installed in /usr/local/lib/, and the libSOIL headers will be installed in /usr/local/include/SOIL/.  An OS X framework can also be generated on request.

# Installing
  - Download/install CMake from <www.cmake.org> or package manager
    CMake is like autoconf in that it will create build scripts which are then 
    used for the actual compilation.

  -	Move into the *build* directory.  

  - Create a build system using the -G option for example:
 
		cmake .. -G Xcode
		cmake .. -G "Unix Makefiles"
	
  - There are some options for cmake builds:
      * CMAKE_INSTALL_PREFIX: default '/usr/local', the installation path.
      * CMAKE_INSTALL_NAME_DIR: if you install outside a standard ld search 
        path, then you should set this to the installation lib/framework path. 
      * CMAKE_BUILD_TYPE: default 'Release', can include debug symbols with
        either 'Debug' or 'RelWithDebInfo'.
        
  - Run 'cmake' with desired options of the form -DOPTION=VALUE
    By default this will create the usual Makefile build system, but CMake can 
    also produce Xcode project files.  See 'cmake --help' to see 
    what "generators" are available in your environment, selected via '-G'.
    	
    For example:
    	
    	cmake .. -DCMAKE_INSTALL_PREFIX=/sw \
                 -DCMAKE_INSTALL_NAME_DIR=/sw \
           
  - Assuming using the default Makefile output from cmake, run 'make' to build 
    and then 'make install'.  You may also build specific targets individually:
      * SOIL_Static builds a static library.
      * SOIL_Dylib builds a shared library.
      * SOIL_Framework builds an OS X framework.  This target is not built by
        default and must be built explicitly.

# License
Public domain.
