# Options
#
# CMAKE_INSTALL_PREFIX: default '/usr/local', the installation path.
# CMAKE_INSTALL_NAME_DIR: if you install outside a standard ld search 
#        path, then you should set this to the installation lib/framework path. 
# CMAKE_BUILD_TYPE: default 'Release', can include debug symbols with
#        either 'Debug' or 'RelWithDebInfo'.
# 

cmake_minimum_required (VERSION 2.8)
PROJECT(SOIL C)
SET(SOIL_VERSION 1.07)


# -------------------------------------------
# User Options
# -------------------------------------------
IF(NOT CMAKE_BUILD_TYPE)
    SET(CMAKE_BUILD_TYPE "Release")
ENDIF(NOT CMAKE_BUILD_TYPE)


# -------------------------------------------
# Configure some Apple specific build options
# -------------------------------------------
IF(APPLE)
    SET(CMAKE_OSX_ARCHITECTURES x86_64;i386)
    SET(CMAKE_OSX_DEPLOYMENT_TARGET 10.7)
    SET(CMAKE_XCODE_ATTRIBUTE_SUPPORTED_PLATFORMS macosx)
    SET(CMAKE_XCODE_ATTRIBUTE_VALID_ARCHS "i386 x86_64")

    # Xcode Generator
    IF(XCODE_VERSION)
        SET(CMAKE_OSX_SYSROOT macosx)
    # Makefiles or other
	# Need the full path to the SDK.
    ELSE(XCODE_VERSION)
        execute_process(COMMAND xcodebuild -sdk macosx -version Path | head -n 1 OUTPUT_VARIABLE CMAKE_OSX_SYSROOT)
        string(REGEX REPLACE "(\r?\n)+$" "" CMAKE_OSX_SYSROOT "${CMAKE_OSX_SYSROOT}")
    ENDIF(XCODE_VERSION)
ENDIF(APPLE)


# -------------------------------------------
# Libraries
# -------------------------------------------
IF(APPLE)
    FIND_LIBRARY(COREFOUNDATION_LIBRARY NAMES CoreFoundation)
    FIND_LIBRARY(OPENGL_LIBRARY NAMES OpenGL)
ENDIF(APPLE)


# -------------------------------------------
# Files
# -------------------------------------------

# Source Files
SET(SOIL_SRCS
	src/image_helper.c
    src/stb_image_aug.c
    src/image_DXT.c
    src/SOIL.h
)

# Header Files
SET(SOIL_HDRS
	src/image_helper.h
    src/stb_image_aug.h
    src/image_DXT.h
    src/stbi_DDS_aug_c.h
    src/stbi_DDS_aug.h
    src/SOIL.c
)

# -------------------------------------------
# Static Library
# -------------------------------------------
ADD_LIBRARY(SOIL_Static STATIC ${SOIL_SRCS} ${SOIL_HDRS})

# Build Settings
SET_PROPERTY(TARGET SOIL_Static PROPERTY OUTPUT_NAME "SOIL")
SET_PROPERTY(TARGET SOIL_Static PROPERTY VERSION 1.07)
SET_PROPERTY(TARGET SOIL_Static PROPERTY SOVERSION 1)

# Copy Header
IF(XCODE_VERSION)
    SET_PROPERTY(TARGET SOIL_Static PROPERTY PUBLIC_HEADER src/SOIL.h)
ENDIF(XCODE_VERSION)

# Install Rules
install(TARGETS SOIL_Static DESTINATION lib)

# -------------------------------------------
# Dynamic Library
# -------------------------------------------
ADD_LIBRARY(SOIL_Dylib SHARED ${SOIL_SRCS} ${SOIL_HDRS})
TARGET_LINK_LIBRARIES(SOIL_Dylib ${COREFOUNDATION_LIBRARY} ${OPENGL_LIBRARY})

# Build Settings
SET_TARGET_PROPERTIES(SOIL_Dylib PROPERTIES BUILD_WITH_INSTALL_RPATH 1 INSTALL_NAME_DIR "${CMAKE_INSTALL_NAME_DIR}/lib")
SET_PROPERTY(TARGET SOIL_Dylib PROPERTY OUTPUT_NAME "SOIL")
SET_PROPERTY(TARGET SOIL_Dylib PROPERTY VERSION 1.07)
SET_PROPERTY(TARGET SOIL_Dylib PROPERTY SOVERSION 1)

# Copy Header
IF(XCODE_VERSION)
    SET_PROPERTY(TARGET SOIL_Dylib PROPERTY PUBLIC_HEADER src/SOIL.h)
ENDIF(XCODE_VERSION)

# Install Rules
install(TARGETS SOIL_Dylib DESTINATION lib)


# -------------------------------------------
# Framework
# -------------------------------------------
IF(APPLE)
    ADD_LIBRARY(SOIL_Framework SHARED EXCLUDE_FROM_ALL ${SOIL_SRCS} ${SOIL_HDRS})
    TARGET_LINK_LIBRARIES(SOIL_Framework ${COREFOUNDATION_LIBRARY} ${OPENGL_LIBRARY})

    # Build Settings
    SET_TARGET_PROPERTIES(SOIL_Framework PROPERTIES BUILD_WITH_INSTALL_RPATH 1 INSTALL_NAME_DIR "@rpath")
    SET_PROPERTY(TARGET SOIL_Framework PROPERTY SOVERSION 1)
    SET_PROPERTY(TARGET SOIL_Framework PROPERTY VERSION 1.07)
    SET_PROPERTY(TARGET SOIL_Framework PROPERTY FRAMEWORK true)
    SET_PROPERTY(TARGET SOIL_Framework PROPERTY FRAMEWORK_VERSION A)
    SET_PROPERTY(TARGET SOIL_Framework PROPERTY OUTPUT_NAME "SOIL")

    # Copy Header
    SET_PROPERTY(TARGET SOIL_Framework PROPERTY PUBLIC_HEADER src/SOIL.h)
ENDIF(APPLE)


# -------------------------------------------
# Files
# -------------------------------------------
install(FILES src/SOIL.h DESTINATION include/SOIL CONFIGURATIONS Release)
