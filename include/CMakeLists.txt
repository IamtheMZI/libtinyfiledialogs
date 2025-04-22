cmake_minimum_required(VERSION 3.10)
project(tinyfiledialogs LANGUAGES C)

option(BUILD_SHARED_LIBS "Build tinyfiledialogs as a shared library" OFF)

# Interface target for headers only
add_library(tinyfiledialogs_headers INTERFACE)
target_include_directories(tinyfiledialogs_headers INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

# Actual implementation target (src only)
add_library(tinyfiledialogs src/tinyfiledialogs.c)

# Link the interface headers to the implementation
target_link_libraries(tinyfiledialogs PUBLIC tinyfiledialogs_headers)

# Windows-specific system libs
if(WIN32)
  target_link_libraries(tinyfiledialogs PRIVATE comdlg32 ole32 uuid)
endif()

# Visibility for Unix shared libs
if(UNIX AND BUILD_SHARED_LIBS)
  set_target_properties(tinyfiledialogs PROPERTIES
    C_VISIBILITY_PRESET hidden
    VISIBILITY_INLINES_HIDDEN YES
  )
endif()

# Installation
install(TARGETS tinyfiledialogs tinyfiledialogs_headers
  EXPORT tinyfiledialogsTargets
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(DIRECTORY include/ DESTINATION include)

install(EXPORT tinyfiledialogsTargets
  FILE tinyfiledialogsTargets.cmake
  NAMESPACE tinyfiledialogs::
  DESTINATION lib/cmake/tinyfiledialogs
)
