#
# (C) Copyright 2013-2016 Johns Hopkins University (JHU), All Rights Reserved.
#
# --- begin cisst license - do not edit ---
#
# This software is provided "as is" under an open source license, with
# no warranty.  The complete license can be found in license.txt and
# http://www.cisst.org/cisst/license.txt.
#
# --- end cisst license ---

project (sawTextToSpeech)

cmake_minimum_required (VERSION 2.8)

# Core libraries
set (REQUIRED_CISST_LIBRARIES cisstCommon
                              cisstVector
                              cisstOSAbstraction
                              cisstMultiTask
                              cisstParameterTypes)

find_package (cisst 1.0.10 COMPONENTS ${REQUIRED_CISST_LIBRARIES})

if (cisst_FOUND_AS_REQUIRED)

  # load cisst configuration
  include (${CISST_USE_FILE})

  # catkin/ROS paths
  cisst_is_catkin_build (sawTextToSpeech_IS_CATKIN_BUILT)
  if (sawTextToSpeech_IS_CATKIN_BUILT)
    set (sawTextToSpeech_CONFIG_FILE_DIR
         "${CATKIN_DEVEL_PREFIX}/share/sawTextToSpeech/cmake")
    set (LIBRARY_OUTPUT_PATH "${sawTextToSpeech_BINARY_DIR}/lib")
  else ()
    set (sawTextToSpeech_CONFIG_FILE_DIR "${sawTextToSpeech_BINARY_DIR}")
  endif ()

  # create/configure file for find_package (sawTextToSpeech)
  set (sawTextToSpeech_INCLUDE_DIR
       "${sawTextToSpeech_SOURCE_DIR}/include"
       "${sawTextToSpeech_BINARY_DIR}/include")
  set (sawTextToSpeech_LIBRARY_DIR "${LIBRARY_OUTPUT_PATH}")
  set (sawTextToSpeech_LIBRARIES sawTextToSpeech)

  # Set the version number
  set (sawTextToSpeech_VERSION_MAJOR "1")
  set (sawTextToSpeech_VERSION_MINOR "2")
  set (sawTextToSpeech_VERSION_PATCH "0")
  set (sawTextToSpeech_VERSION "${sawTextToSpeech_VERSION_MAJOR}.${sawTextToSpeech_VERSION_MINOR}.${sawTextToSpeech_VERSION_PATCH}")

  # Generate sawTextToSpeechRevision.h
  configure_file ("${sawTextToSpeech_SOURCE_DIR}/code/sawTextToSpeechRevision.h.in"
                  "${sawTextToSpeech_BINARY_DIR}/include/sawTextToSpeech/sawTextToSpeechRevision.h")

  include_directories (${sawTextToSpeech_INCLUDE_DIR})
  set (sawTextToSpeech_HEADER_DIR "${sawTextToSpeech_SOURCE_DIR}/include/sawTextToSpeech")
  add_library (sawTextToSpeech
               code/mtsTextToSpeech.cpp
               ${sawTextToSpeech_HEADER_DIR}/sawTextToSpeechExport.h
               ${sawTextToSpeech_HEADER_DIR}/mtsTextToSpeech.h
               ${sawTextToSpeech_BINARY_DIR}/include/sawTextToSpeech/sawTextToSpeechRevision.h)

  set_property (TARGET sawTextToSpeech PROPERTY FOLDER "sawTextToSpeech")
  cisst_target_link_libraries (sawTextToSpeech ${REQUIRED_CISST_LIBRARIES})

  set (sawTextToSpeech_CMAKE_CONFIG_FILE
       "${sawTextToSpeech_CONFIG_FILE_DIR}/sawTextToSpeechConfig.cmake")

  configure_file ("${CMAKE_CURRENT_SOURCE_DIR}/cmake/sawTextToSpeechConfig.cmake.in"
                  "${sawTextToSpeech_CMAKE_CONFIG_FILE}"
                   @ONLY)

  cisst_add_config_version ("${sawTextToSpeech_CONFIG_FILE_DIR}/sawTextToSpeechConfigVersion.cmake"
                            VERSION ${sawTextToSpeech_VERSION}
                            DESTINATION "share/sawTextToSpeech"
                            COMPONENT sawTextToSpeech)

  # Install target for headers and library
  install (DIRECTORY "${sawTextToSpeech_SOURCE_DIR}/include/sawTextToSpeech"
                     "${sawTextToSpeech_BINARY_DIR}/include/sawTextToSpeech"
           DESTINATION include
           PATTERN .svn EXCLUDE)

  install (TARGETS sawTextToSpeech
           RUNTIME DESTINATION bin
           LIBRARY DESTINATION lib
           ARCHIVE DESTINATION lib)

  install (FILES "${sawTextToSpeech_CMAKE_CONFIG_FILE}"
           DESTINATION "share/sawTextToSpeech")

else (cisst_FOUND_AS_REQUIRED)
  message ("Information: code in ${CMAKE_CURRENT_SOURCE_DIR} will not be compiled, it requires ${REQUIRED_CISST_LIBRARIES}")
endif (cisst_FOUND_AS_REQUIRED)
