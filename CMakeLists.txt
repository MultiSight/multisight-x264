cmake_minimum_required(VERSION 2.8)
project(x264)

include(common.cmake NO_POLICY_SCOPE)

include(ExternalProject)

# --prefix below is used to tell the project where to install to. We install to a directory under build for 2 reasons:
# 1) by having the external project install to a directory in build, we can support rebuilds that just install
# the existing libraries. In effect, we have a 2 step install: the projects internal build system installs to --prefix
# and our build installs FROM --prefix to ${devel_artifacts_path}
# 2) the other reason to have the external project install to a directory under prefix is that a clean can be
# achieved by simply deleting build directory.

IF(BUILD_WINDOWS)

ExternalProject_Add(
  x264_windows,
  URL ${CMAKE_CURRENT_SOURCE_DIR}/x264.tar.bz2
  SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/x264_sources
  CONFIGURE_COMMAND ${CMAKE_CURRENT_BINARY_DIR}/x264_sources/configure --host=i686-w64-mingw32 --cross-prefix=i686-w64-mingw32- --prefix=${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts --enable-shared --disable-avs --disable-cli --disable-gpl --disable-opencl
  PREFIX ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts
  INSTALL_COMMAND make install COMMAND make install-lib-dev COMMAND make install-lib-static
)

ELSE()

  if(CMAKE_SYSTEM MATCHES "Linux-")

    ExternalProject_Add(
      x264_linux
      URL ${CMAKE_CURRENT_SOURCE_DIR}/x264.tar.bz2
      SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/x264_sources
      CONFIGURE_COMMAND ${CMAKE_CURRENT_BINARY_DIR}/x264_sources/configure --prefix=${CMAKE_CURRENT_BINARY_DIR}/artifacts --enable-shared --disable-avs --disable-cli --disable-gpl
      PREFIX ${CMAKE_CURRENT_BINARY_DIR}/artifacts
      INSTALL_COMMAND make install COMMAND make install-lib-dev COMMAND make install-lib-static
    )

  ENDIF()

ENDIF()

SET(CMAKE_INSTALL_PREFIX ${devel_artifacts_path})

IF(CMAKE_SYSTEM MATCHES "Linux-")

  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/artifacts/include/x264.h DESTINATION include/x264)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/artifacts/include/x264_config.h DESTINATION include/x264)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/artifacts/lib/libx264.a DESTINATION lib)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/artifacts/lib/libx264.so DESTINATION lib)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/artifacts/lib/libx264.so.142 DESTINATION lib)

ELSE()

  install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts/include/x264.h DESTINATION include/x264)
  install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts/include/x264_config.h DESTINATION include/x264)
  install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts/bin/libx264-142.dll DESTINATION lib)
  install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts/bin/libx264-142.lib DESTINATION lib)

ENDIF()
