#
#
# (C) Copyright 2013-2018 Johns Hopkins University (JHU), All Rights Reserved.
#
# --- begin cisst license - do not edit ---
#
# This software is provided "as is" under an open source license, with
# no warranty.  The complete license can be found in license.txt and
# http://www.cisst.org/cisst/license.txt.
#
# --- end cisst license ---

set (REQUIRED_CISST_LIBRARIES
     cisstCommon
     cisstVector
     cisstVectorQt
     cisstNumerical
     cisstOSAbstraction
     cisstMultiTask
     cisstMultiTaskQt
     cisstParameterTypes
     cisstParameterTypesQt
     cisstQt)

# cisst
find_package (cisst REQUIRED ${REQUIRED_CISST_LIBRARIES})

if (cisst_FOUND_AS_REQUIRED)

  # cisst
  include (${CISST_USE_FILE})

  # for Qt generated header files
  include_directories (${CMAKE_CURRENT_BINARY_DIR})
  if (CISST_HAS_QT4)
    qt4_wrap_cpp (SAW_ATINETFT_QT_WRAP_CPP
                  ${sawOptoforceSensor_HEADER_DIR}/mtsForceTorqueQtWidget.h
                  )
  else (CISST_HAS_QT4)
    set (CMAKE_AUTOMOC ON)
  endif (CISST_HAS_QT4)

  add_library (sawOptoforceSensorQt
               ${sawOptoforceSensor_HEADER_DIR}/sawOptoforceSensorQtExport.h
               ${sawOptoforceSensor_HEADER_DIR}/mtsForceTorqueQtWidget.h
               mtsForceTorqueQtWidget.cpp
               ${SAW_ATINETFT_QT_WRAP_CPP})
  set_property (TARGET sawOptoforceSensorQt PROPERTY FOLDER "sawOptoforceSensor") 
  cisst_target_link_libraries (sawOptoforceSensorQt ${REQUIRED_CISST_LIBRARIES})

  # make sure the new library is known by the parent folder to add to the config file
  set (sawOptoforceSensorQt_LIBRARIES sawOptoforceSensorQt PARENT_SCOPE)

else (cisst_FOUND_AS_REQUIRED)
  message ("Information: code will not be compiled, it requires ${REQUIRED_CISST_LIBRARIES}")
endif (cisst_FOUND_AS_REQUIRED)
