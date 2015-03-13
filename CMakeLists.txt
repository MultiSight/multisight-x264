cmake_minimum_required(VERSION 2.8)
project(x264)

include(common.cmake NO_POLICY_SCOPE)

include(ExternalProject)

IF(CMAKE_SYSTEM MATCHES "Windows")
    # no build on windows...
ELSE()
    ExternalProject_Add(
      x264_linux
      URL ${CMAKE_CURRENT_SOURCE_DIR}/x264.tar.bz2
      SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/x264_sources
      CONFIGURE_COMMAND ${CMAKE_CURRENT_BINARY_DIR}/x264_sources/configure --prefix=${CMAKE_CURRENT_BINARY_DIR}/artifacts --enable-shared --disable-avs --disable-cli --disable-gpl
      PREFIX ${CMAKE_CURRENT_BINARY_DIR}/artifacts
      INSTALL_COMMAND make install COMMAND make install-lib-dev COMMAND make install-lib-static
    )
ENDIF()

SET(CMAKE_INSTALL_PREFIX ${devel_artifacts_path})

IF(CMAKE_SYSTEM MATCHES "Windows")
    install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts/include/x264.h DESTINATION include/x264)
    install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts/include/x264_config.h DESTINATION include/x264)
    install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts/bin/libx264-142.dll DESTINATION lib)
    install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/x264-Win64-artifacts/bin/libx264-142.lib DESTINATION lib)
ELSE()
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/artifacts/include/x264.h DESTINATION include/x264)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/artifacts/include/x264_config.h DESTINATION include/x264)
    install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/artifacts/lib/ DESTINATION lib FILES_MATCHING PATTERN "*")
ENDIF()
