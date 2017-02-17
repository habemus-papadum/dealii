## ---------------------------------------------------------------------
##
## Copyright (C) 2012 - 2016 by the deal.II authors
##
## This file is part of the deal.II library.
##
## The deal.II library is free software; you can use it, redistribute
## it, and/or modify it under the terms of the GNU Lesser General
## Public License as published by the Free Software Foundation; either
## version 2.1 of the License, or (at your option) any later version.
## The full text of the license can be found in the file LICENSE at
## the top level of the deal.II distribution.
##
## ---------------------------------------------------------------------

IF(DEAL_II_COMPONENT_EXAMPLES)
  MESSAGE(STATUS "Setting up examples")

  INSTALL(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/
    DESTINATION ${DEAL_II_EXAMPLES_RELDIR}
    COMPONENT examples
    FILES_MATCHING
    #
    # Exclude folder structures: doc, doxygen, CMakeFiles,...
    #
    PATTERN "CMakeFiles*" EXCLUDE
    PATTERN "doc*" EXCLUDE
    #
    # Glob Includes:
    #
    PATTERN "*.cc"
    PATTERN "*.prm"
    PATTERN "*.inp"
    PATTERN "step*/CMakeLists.txt"
    PATTERN "doxygen/CMakeLists.txt"
    #
    # Special files:
    #
    PATTERN "output.reference.dat"        # step-39
    PATTERN "postprocess.pl"              # step-39
    PATTERN "obstacle.pbm"                # step-42
    PATTERN "untitled.geo"                # step-49
    PATTERN "untitled.msh"                # step-49
    PATTERN "topography.txt.gz"           # step-53
    PATTERN "input/initial_mesh_3d.vtk"   # step-54
    PATTERN "DTMB-5415_bulbous_bow.iges"  # step-54
    )

  MESSAGE(STATUS "Setting up examples - Done")
ENDIF()

#
# Add a dummy target to make files known to IDEs like qtcreator
#

FILE(GLOB _misc
  ${CMAKE_CURRENT_SOURCE_DIR}/step-*/*.cc
  ${CMAKE_CURRENT_SOURCE_DIR}/step-*/*.h
)
ADD_CUSTOM_TARGET(dummy_examples_files
  SOURCES ${_misc}
  )

FILE(GLOB steps ${CMAKE_CURRENT_SOURCE_DIR}/step-*/)

foreach(step_path ${steps})
  get_filename_component(step ${step_path} NAME)
  add_executable(${step} ${step_path}/${step}.cc)
  set_target_properties(${step} PROPERTIES EXCLUDE_FROM_ALL TRUE)

  set(_build ${DEAL_II_BUILD_TYPES})
  SET_PROPERTY(TARGET ${step} APPEND PROPERTY
          INCLUDE_DIRECTORIES
          "${CMAKE_BINARY_DIR}/include"
          "${CMAKE_SOURCE_DIR}/include"
          "${CMAKE_SOURCE_DIR}/include/deal.II/")
  SET_TARGET_PROPERTIES(${step} PROPERTIES
          #LINK_FLAGS " ${DEAL_II_LINKER_FLAGS_${_build}}"
          COMPILE_DEFINITIONS "${DEAL_II_DEFINITIONS};${DEAL_II_DEFINITIONS_${_build}}"
          COMPILE_FLAGS "${DEAL_II_CXX_FLAGS} ${DEAL_II_CXX_FLAGS_${_build}}"
          LINKER_LANGUAGE "CXX"
          RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/${_test_short}"
          )
  TARGET_LINK_LIBRARIES(${step}
            ${DEAL_II_BASE_NAME}${DEAL_II_${_build}_SUFFIX})

endforeach()
