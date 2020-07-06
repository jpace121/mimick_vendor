cmake_minimum_required(VERSION 3.5)

project(mimick_vendor)

find_package(ament_cmake REQUIRED)

macro(build_mimick)

  set(extra_cmake_args)
  if(DEFINED CMAKE_BUILD_TYPE)
    list(APPEND extra_cmake_args -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE})
  endif()

  if(DEFINED CMAKE_TOOLCHAIN_FILE)
    list(APPEND extra_cmake_args "-DCMAKE_TOOLCHAIN_FILE=${CMAKE_TOOLCHAIN_FILE}")
    if(ANDROID)
      if(DEFINED ANDROID_ABI)
        list(APPEND extra_cmake_args "-DANDROID_ABI=${ANDROID_ABI}")
      endif()
      if(DEFINED ANDROID_CPP_FEATURES)
        list(APPEND extra_cmake_args "-DANDROID_CPP_FEATURES=${ANDROID_CPP_FEATURES}")
      endif()
      if(DEFINED ANDROID_FUNCTION_LEVEL_LINKING)
        list(APPEND extra_cmake_args "-DANDROID_FUNCTION_LEVEL_LINKING=${ANDROID_FUNCTION_LEVEL_LINKING}")
      endif()
      if(DEFINED ANDROID_NATIVE_API_LEVEL)
        list(APPEND extra_cmake_args "-DANDROID_NATIVE_API_LEVEL=${ANDROID_NATIVE_API_LEVEL}")
      endif()
      if(DEFINED ANDROID_NDK)
        list(APPEND extra_cmake_args "-DANDROID_NDK=${ANDROID_NDK}")
      endif()
      if(DEFINED ANDROID_STL)
        list(APPEND extra_cmake_args "-DANDROID_STL=${ANDROID_STL}")
      endif()
      if(DEFINED ANDROID_TOOLCHAIN_NAME)
        list(APPEND extra_cmake_args "-DANDROID_TOOLCHAIN_NAME=${ANDROID_TOOLCHAIN_NAME}")
      endif()
    endif()
  else()
    list(APPEND extra_cmake_args "-DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}")
  endif()
  
  # list(APPEND extra_cmake_args "-DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}")
  # list(APPEND extra_cmake_args "-DSPDLOG_BUILD_TESTS=OFF")
  # list(APPEND extra_cmake_args "-DSPDLOG_BUILD_EXAMPLE=OFF")
  
  # if(NOT WIN32)
  #   # TODO(karsten1987): re-address this once upstream ticket is resolved:
  #   # https://github.com/fmtlib/fmt/issues/1604
  #   list(APPEND extra_cmake_args "-DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS} -Wno-sign-conversion")
  #   list(APPEND extra_cmake_args "-DSPDLOG_BUILD_SHARED=ON")
  # endif()

  include(ExternalProject)
  # Get Mimick ros2 fork
  # externalproject_add(mimick-ext
  #   URL https://github.com/Snaipe/Mimick/archive/v0.3.0.zip
  #   # GIT_TAG 5a07c19
  #   # DOWNLOAD_NO_EXTRACT TRUE
  #   TIMEOUT 6000
  #   CMAKE_ARGS
  #     -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install
  #     ${extra_cmake_args}
  # )

  externalproject_add(mimick-ext
    GIT_REPOSITORY https://github.com/Blast545/Mimick
    GIT_TAG exportTargets
    TIMEOUT 6000
    CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install
    ${extra_cmake_args}
  )

  # The external project will install to the build folder, but we'll install that on make install.
  install(
    DIRECTORY
      ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install
    DESTINATION
      ${CMAKE_INSTALL_PREFIX}
  )
  # message(STATUS "Finishing install to ${CMAKE_INSTALL_PREFIX}")

endmacro()

build_mimick()

ament_export_libraries(mimick)
ament_export_dependencies(mimick)

# TODO(blast545): This section and package.xml should be updated once copyright auto is solved. See: https://github.com/ament/ament_lint/issues/237
if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

# list(APPEND mimick_vendor_TARGETS mimick)
# list(APPEND mimick_vendor_TARGETS mimick)

# this ensures that the package has an environment hook setting the PATH
ament_package()