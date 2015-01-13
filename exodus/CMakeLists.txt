cmake_minimum_required(VERSION 2.6)
PROJECT(ExodusII C CXX Fortran)

SET(EXODUSII_VERSION_MAJOR "6")
SET(EXODUSII_VERSION_MINOR "09")
SET(EXODUSII_VERSION_PATCH "0")
SET(EXODUSII_VERSION "${EXODUSII_VERSION_MAJOR}.${EXODUSII_VERSION_MINOR}")
SET(EXODUSII_VERSION_FULL "${EXODUSII_VERSION}.${EXODUSII_VERSION_PATCH}")

add_subdirectory(cbind)
add_subdirectory(forbind)

find_path( NETCDF_INCLUDE_DIR NAMES netcdf.h 
	   PATHS
	   $ENV{NETCDF_DIR}/include
	   $ENV{NETCDF_DIR}/libsrc
	   $ENV{ACCESS}/include
	   NO_DEFAULT_PATH
	 )	
find_path( NETCDF_INCLUDE_DIR NAMES netcdf.h)

find_library( NETCDF_LIBRARY
	      NAMES netcdf
	      PATHS "$ENV{NETCDF_DIR}/lib"
		    "$ENV{NETCDF_DIR}/libsrc/.libs"
		    "$ENV{ACCESS}/lib"
              NO_DEFAULT_PATH)
find_library( NETCDF_LIBRARY NAMES netcdf)

find_program (NETCDF_NCDUMP ncdump
   $ENV{ACCESS}/bin
   $ENV{NETCDF_DIR}/bin
   $ENV{NETCDF_DIR}/ncdump
)

# Hack for HDF5
find_library( HDF5_LIBRARY NAMES hdf5
  $ENV{HDF5_DIR}/lib
  $ENV{HDF5_DIR}/libsrc/.libs
  $ENV{ACCESS}/lib/shared
  $ENV{ACCESS}/lib
  /usr/local/hdf5/lib
  NO_DEFAULT_PATH
  )
find_library( HDF5_LIBRARY NAMES hdf5)

find_library( HDF5HL_LIBRARY NAMES hdf5_hl
  $ENV{HDF5_DIR}/lib
  $ENV{HDF5_DIR}/libsrc/.libs
  $ENV{ACCESS}/lib/shared
  $ENV{ACCESS}/lib
  /usr/local/hdf5/lib
  NO_DEFAULT_PATH
  )
find_library( HDF5HL_LIBRARY NAMES hdf5_hl)

# Check for ZLib, but only if using HDF5.
FIND_PACKAGE(ZLIB)
IF(NOT ZLIB_LIBRARY)
   MESSAGE(FATAL_ERROR "HDF5 Support specified, cannot find ZLib.")
ENDIF()
SET(USE_ZLIB ON)
INCLUDE_DIRECTORIES(${ZLIB_INCLUDE_DIRS})

# Check for curl. Only needed if using an installed netcdf built with curl.
FIND_PACKAGE(CURL)
IF(CURL_FOUND)
   SET(CURL ${CURL_LIBRARY})
ENDIF()


find_program(PYTHON "python")

if (PYTHON)
  set(EXODUS_PY_IN "${CMAKE_CURRENT_SOURCE_DIR}/exodus.py.in")
  set(EXODUS_PY "${CMAKE_CURRENT_BINARY_DIR}/exodus.py")

  configure_file(${EXODUS_PY_IN} ${EXODUS_PY} @ONLY)

  INSTALL(FILES ${EXODUS_PY} DESTINATION ${PYTHON_INSTALL})

endif()
