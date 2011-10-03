cmake_minimum_required(VERSION 2.6)
PROJECT(Nemesis)

SET(NEMESIS_VERSION_MAJOR "3")
SET(NEMESIS_VERSION_MINOR "09")
SET(NEMESIS_VERSION_PATCH "0")
SET(NEMESIS_VERSION "${NEMESIS_VERSION_MAJOR}.${NEMESIS_VERSION_MINOR}")
SET(NEMESIS_VERSION_FULL "${NEMESIS_VERSION}.${NEMESIS_VERSION_PATCH}")

SET(NEMESIS_BUILD_SHARED_LIBS "${BUILD_SHARED_LIBS}")

SET(HEADERS "ne_nemesisI.h")

find_path( NETCDF_INCLUDE_DIR netcdf.h
   $ENV{ACCESS}/inc
   $ENV{NETCDF_DIR}/inc
   $ENV{NETCDF_DIR}/include
   $ENV{NETCDF_DIR}/libsrc
   )	

find_library( NETCDF_LIBRARY netcdf
  $ENV{ACCESS}/lib/shared
  $ENV{ACCESS}/lib
  $ENV{NETCDF_DIR}/lib
  $ENV{NETCDF_DIR}/libsrc/.libs
  )

find_program (NETCDF_NCDUMP ncdump
   $ENV{ACCESS}/bin
   $ENV{NETCDF_DIR}/bin
   $ENV{NETCDF_DIR}/ncdump
   )

find_library( EXODUS_LIBRARY exodus
  $ENV{ACCESS}/lib/shared
  $ENV{ACCESS}/lib
  )

# Hack for HDF5
find_library( HDF5_LIBRARY hdf5
  $ENV{ACCESS}/lib/shared
  $ENV{ACCESS}/lib
  $ENV{NETCDF_DIR}/lib
  $ENV{NETCDF_DIR}/libsrc/.libs
  /usr/local/hdf5/lib
  )

find_library( HDF5HL_LIBRARY hdf5_hl
  $ENV{ACCESS}/lib/shared
  $ENV{ACCESS}/lib
  $ENV{NETCDF_DIR}/lib
  $ENV{NETCDF_DIR}/libsrc/.libs
  /usr/local/hdf5/lib
  )

find_library(Z_LIBRARY z
  /usr/local/zlib/lib
  )

find_library(MATH_LIBRARY
             NAMES m
             PATHS /usr/lib64
                   /usr/lib
)

INCLUDE(CheckIncludeFile)
CHECK_INCLUDE_FILE( malloc.h EX_HAVE_MALLOC_H )

INCLUDE_DIRECTORIES(
  "${CMAKE_CURRENT_SOURCE_DIR}"
  "${CMAKE_CURRENT_BINARY_DIR}"
  "${CMAKE_CURRENT_SOURCE_DIR}/../exodusii/cbind/include/"
  "${CMAKE_CURRENT_BINARY_DIR}/../exodusii/cbind/include/"
  "${NETCDF_INCLUDE_DIR}"
)

SET(SOURCES
ne_gcmp.c
ne_gebig.c
ne_gelcm.c
ne_gelm.c
ne_gelt.c
ne_gennm.c
ne_genv.c
ne_giinf.c
ne_ginig.c
ne_glbp.c
ne_gncm.c
ne_gncor.c
ne_gneat.c
ne_gnelc.c
ne_gnm.c
ne_gnnnm.c
ne_gnns.c
ne_gnnsd.c
ne_gnnv.c
ne_gnspg.c
ne_gnss.c
ne_gnssd.c
ne_gsspg.c
ne_pcmp.c
ne_pcmpc.c
ne_pebig.c
ne_pelcm.c
ne_pelm.c
ne_pevs.c
ne_pennm.c
ne_piinf.c
ne_pinig.c
ne_plbp.c
ne_plbpc.c
ne_pncm.c
ne_pncor.c
ne_pneat.c
ne_pnelc.c
ne_pnm.c
ne_pnnnm.c
ne_pnns.c
ne_pnnsd.c
ne_pnspg.c
ne_pnss.c
ne_pnssd.c
ne_pnvs.c
ne_psspg.c
ne_util.c
)

ADD_LIBRARY(nemesis ${SOURCES})
TARGET_LINK_LIBRARIES(nemesis ${EXODUS_LIBRARY} ${NETCDF_LIBRARY} ${HDF5HL_LIBRARY} ${HDF5_LIBRARY} ${Z_LIBRARY} ${MATH_LIBRARY})

ADD_EXECUTABLE(ne_test ne_test.c)
TARGET_LINK_LIBRARIES( ne_test nemesis ${EXODUS_LIBRARY} ${NETCDF_LIBRARY} ${HDF5HL_LIBRARY} ${HDF5_LIBRARY} ${Z_LIBRARY} ${MATH_LIBRARY})

INSTALL(TARGETS nemesis
  RUNTIME DESTINATION lib COMPONENT Runtime
  LIBRARY DESTINATION lib COMPONENT Runtime
  ARCHIVE DESTINATION lib COMPONENT Development)
INSTALL(FILES ${HEADERS} DESTINATION include COMPONENT Development)
