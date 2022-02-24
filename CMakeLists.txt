cmake_minimum_required(VERSION 3.5)
project(socketcan-plus-plus
  VERSION 1.1.0
  LANGUAGES C CXX
)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

add_library(${PROJECT_NAME} SHARED
  src/socket_can_common.cpp
  src/socket_can_id.cpp
  src/socket_can_receiver.cpp
  src/socket_can_sender.cpp
)

target_include_directories(${PROJECT_NAME}
  PUBLIC
    # Different paths for includes for build and install phase supported
    # via INSTALL_INTERFACE and BUILD_INTERFACE generator expressions.
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

set_target_properties(${PROJECT_NAME} PROPERTIES
  VERSION ${socketcan-plus-plus_VERSION}
  SOVERSION ${socketcan-plus-plus_VERSION_MAJOR}
  PUBLIC_HEADER "include/socketcan-plus-plus/socketcan-plus-plus.hpp"
)

#if(BUILD_TESTING)
#  ament_add_gtest(${PROJECT_NAME}_test
#    test/gtest_main.cpp
#    test/receiver.cpp
#    test/sanity_checks.cpp)
#  target_include_directories(${PROJECT_NAME}_test PUBLIC include)
#  target_link_libraries(${PROJECT_NAME}_test ${PROJECT_NAME})
#endif()

set(CMAKE_INSTALL_CMAKEDIR share/socketcan-plus-plus/cmake)

install(DIRECTORY include/socketcan-plus-plus/
  DESTINATION include/${PROJECT_NAME}
)

install(
  TARGETS ${PROJECT_NAME}
  EXPORT socketcan-plus-plusTargets
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
  PUBLIC_HEADER DESTINATION include
)

install(EXPORT socketcan-plus-plusTargets
  FILE socketcan-plus-plusTargets.cmake
  NAMESPACE SocketcanPlusPlus::
  DESTINATION ${CMAKE_INSTALL_CMAKEDIR}
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(socketcan-plus-plusConfigVersion.cmake
  COMPATIBILITY AnyNewerVersion
)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/socketcan-plus-plusConfigVersion.cmake
  DESTINATION ${CMAKE_INSTALL_CMAKEDIR}
)

install(FILES socketcan-plus-plusConfig.cmake
  DESTINATION ${CMAKE_INSTALL_CMAKEDIR}
)

CONFIGURE_FILE(
  "${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
  IMMEDIATE @ONLY
)

ADD_CUSTOM_TARGET(uninstall
  "${CMAKE_COMMAND}" -P "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
)
