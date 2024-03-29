CMAKE_MINIMUM_REQUIRED(VERSION 3.5 FATAL_ERROR)

PROJECT(OpenscenegraphThirdParty)

# Only enable release and debug builds
IF(CMAKE_CONFIGURATION_TYPES)
  SET(CMAKE_CONFIGURATION_TYPES Debug Release)
  SET(CMAKE_CONFIGURATION_TYPES "${CMAKE_CONFIGURATION_TYPES}" CACHE STRING
    "Reset the configurations to what we need"
    FORCE)
ENDIF()

# Generate folder name for install
IF(${CMAKE_SIZEOF_VOID_P} EQUAL 8)
  SET(BITS "x64")
ELSEIF (${CMAKE_SIZEOF_VOID_P} EQUAL 4)
  SET(BITS "x86")
ENDIF()

IF (MSVC)
  IF (${MSVC_VERSION} EQUAL 1600)
    SET(TOOLSET "v100") # Visual Studio 2010
  ELSEIF (${MSVC_VERSION} EQUAL 1700)
    SET(TOOLSET "v110") # Visual Studio 2012
  ELSEIF (${MSVC_VERSION} EQUAL 1800)
    SET(TOOLSET "v120") # Visual Studio 2013
  ELSEIF (${MSVC_VERSION} EQUAL 1900)
    SET(TOOLSET "v140") # Visual Studio 2015
  ELSEIF (${MSVC_VERSION} GREATER 1910 AND ${MSVC_VERSION} LESS 1920)
    SET(TOOLSET "v141") # Visual Studio 2017
  ENDIF()
ENDIF()

IF(MINGW)
  SET(TOOLSET "mingw")
ENDIF()

SET(dirname "${TOOLSET}-${BITS}")

# Set directories if not set explicitly
IF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  SET(CMAKE_INSTALL_PREFIX "${PROJECT_BINARY_DIR}/3rdParty/${dirname}" CACHE "Force change of path" PATH FORCE)
ENDIF()

SET(ZLIB_SOURCE_DIR CACHE PATH "Path where to find ZLIB source")
SET(LIBPNG_SOURCE_DIR CACHE PATH "Path where to find LIBPNG source")
SET(LIBJPEG_SOURCE_DIR CACHE PATH "Path where to find LIBJPEG source")
SET(LIBTIFF_SOURCE_DIR CACHE PATH "Path where to find LIBTIFF source")
SET(FREETYPE_SOURCE_DIR CACHE PATH "Path where to find FREETYPE source")
SET(GLUT_SOURCE_DIR CACHE PATH "Path where to find GLUT source")
SET(GIFLIB_SOURCE_DIR CACHE PATH "Path where to find GIFLIB source")
SET(MINIZIP_SOURCE_DIR CACHE PATH "Path where to find MINIZIP source")
SET(CURL_SOURCE_DIR CACHE PATH "Path where to find cURL source")

# Check dependencies for LIBPNG
IF (LIBPNG_SOURCE_DIR)
  IF(NOT ZLIB_SOURCE_DIR)
    MESSAGE(FATAL_ERROR "Error: LIBPNG depends on ZLIB and no ZLIB path has been set")
  ENDIF()
ENDIF ()

# Check dependencies for LIBTIFF
IF(LIBTIFF_SOURCE_DIR)
  IF(NOT ZLIB_SOURCE_DIR)
    MESSAGE(FATAL_ERROR "Error: LIBTIFF depends on ZLIB and no ZLIB path has been set")
  ENDIF()

  IF(NOT LIBJPEG_SOURCE_DIR)
    MESSAGE(FATAL_ERROR "Error: LIBTIFF depends on LIBJPEG and no LIBJPEG path has been set")
  ENDIF()
ENDIF()

# Check dependencies for MINIZIP
IF (MINIZIP_SOURCE_DIR)
  IF(NOT ZLIB_SOURCE_DIR)
    MESSAGE(FATAL_ERROR "Error: MINIZIP depends on ZLIB and no ZLIB path has been set")
  ENDIF()
ENDIF ()

# Add libraries
IF (ZLIB_SOURCE_DIR)
  ADD_SUBDIRECTORY(zlib)
ENDIF()

IF (LIBPNG_SOURCE_DIR)
  ADD_SUBDIRECTORY(libpng)
ENDIF()

IF (LIBJPEG_SOURCE_DIR)
  ADD_SUBDIRECTORY(libjpeg)
ENDIF()

IF (LIBTIFF_SOURCE_DIR)
  ADD_SUBDIRECTORY(libtiff)
ENDIF()

IF (FREETYPE_SOURCE_DIR)
  ADD_SUBDIRECTORY(freetype)
ENDIF()

IF (GLUT_SOURCE_DIR)
  ADD_SUBDIRECTORY(glut)
ENDIF()

IF (GIFLIB_SOURCE_DIR)
  ADD_SUBDIRECTORY(giflib)
ENDIF()

IF (MINIZIP_SOURCE_DIR)
  ADD_SUBDIRECTORY(minizip)
ENDIF()

IF (CURL_SOURCE_DIR)
  ADD_SUBDIRECTORY(curl)
ENDIF()
