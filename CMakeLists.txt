cmake_minimum_required(VERSION 3.0)
project(splat VERSION 1.4.2)

include (InstallRequiredSystemLibraries)
set (CPACK_RESOURCE_FILE_LICENSE  
     "${CMAKE_CURRENT_SOURCE_DIR}/COPYING")
set (CPACK_PACKAGE_VERSION_MAJOR "${splat_VERSION_MAJOR}")
set (CPACK_PACKAGE_VERSION_MINOR "${splat_VERSION_MINOR}")
set (CPACK_GENERATOR             ZIP STGZ)
include (CPack)

# -- C/C++ flags.
if ( CMAKE_COMPILER_IS_GNUCC )
  set(CMAKE_C_FLAGS
    "${CMAKE_C_FLAGS} -Wall -Werror -std=gnu99 -fomit-frame-pointer -ffast-math")
  set(CMAKE_CXX_FLAGS
    "${CMAKE_CXX_FLAGS} -Wall -Werror -std=c++11 -fomit-frame-pointer -ffast-math")
  if(${CMAKE_SYSTEM_NAME} STREQUAL Windows)
    set(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS} -static")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -static")
  endif()
endif ( CMAKE_COMPILER_IS_GNUCC )
if ( MSVC )
  set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} /W4")
endif ( MSVC )

## -- Dependencies.
if (WIN32)
  find_library(MATHLIB NAMES libm.a   libm.lib)
  find_library(BZ2LIB  NAMES libbz2.a libbz2.lib)
  find_library(ZLIB    NAMES libz.a   libz.lib)
  set(OSLIBS ${MATHLIB} ${BZ2LIB} ${ZLIB})
else()
  set(OSLIBS m bz2 z)
endif()
add_library(oslibs INTERFACE)
target_link_libraries(oslibs INTERFACE {OSLIBS})
message("** OSLIBS: {OSLIBS}")

## -- SPLAT
set(SPLAT_SRC     itwom3.0.cpp splat.cpp)
set(SPLAT_HEADERS std-parms.h hd-parms.h splat.h)
add_executable(splat-hd  ${SPLAT_SRC} ${SPLAT_HEADERS})
add_executable(splat-std ${SPLAT_SRC} ${SPLAT_HEADERS})
target_link_libraries(splat-hd  ${OSLIBS})
target_link_libraries(splat-std ${OSLIBS})
target_compile_definitions(splat-hd PRIVATE SPLAT_HD)
install(TARGETS splat-hd  DESTINATION bin)
install(TARGETS splat-std DESTINATION bin)
## -- MANUAL PAGE.
install(FILES docs/english/man/splat.1 DESTINATION share/man/es/man1)
install(FILES docs/spanish/man/splat.1 DESTINATION share/man/man1   )
## -- TOOLS.
add_executable(citydecoder utils/citydecoder.c)
add_executable(usgs2sdf    utils/usgs2sdf.c   )
add_executable(srtm2sdf    utils/srtm2sdf.c   )
add_executable(srtm2sdf-hd utils/srtm2sdf.c   )
add_executable(fontdata    utils/fontdata.c   )
target_link_libraries(fontdata    ${OSLIBS})
target_link_libraries(srtm2sdf-hd ${OSLIBS})
target_link_libraries(srtm2sdf    ${OSLIBS})


install(TARGETS citydecoder usgs2sdf srtm2sdf srtm2sdf-hd fontdata DESTINATION bin)
install(FILES utils/postdownload DESTINATION bin RENAME splat-dem2sdf)
install(
  FILES utils/download-from-srtm utils/convert-coordinate
  DESTINATION bin
  PERMISSIONS
  OWNER_READ    OWNER_WRITE OWNER_EXECUTE
  GROUP_READ    GROUP_WRITE GROUP_EXECUTE
  WORLD_EXECUTE             WORLD_READ)
