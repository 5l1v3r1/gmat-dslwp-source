# $Id$
# 
# GMAT: General Mission Analysis Tool.
# 
# CMAKE script file for GMAT Project
# This file must be installed in the GMAT plugins directory
# That is, we should have the directory structure:
#   ./CInterfacePlugin
#   ./DataInterfacePlugin
#   etc... other plugins
#
# Original Author: Ravi Mathur
#
# DO NOT MODIFY THIS FILE UNLESS YOU KNOW WHAT YOU ARE DOING!
#

# Convenience macro used by plugins to enable common functionality
# Inputs:
#   TargetName:  Name of plugin target
#   PLUGIN_DIRS: List of directories containing source/header files
#   PLUGIN_SRCS: List of .c and .cpp source files
#   INSTALL_DIR: Directory to install plugin (plugins, bin, etc.)
# Note that all paths should be relative to the plugin's CMakeLists.txt
MACRO(_SETUPPLUGIN TargetName PLUGIN_DIRS PLUGIN_SRCS INSTALL_DIR)
  # Add Preproccessor Definitions
  ADD_DEFINITIONS("-D_DYNAMICLINK")

  # ====================================================================
  # Recursively find all include files in specified directories
  FOREACH(I_DIR ${PLUGIN_DIRS})
    FILE(GLOB_RECURSE IDIR_HEADERS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ${I_DIR}/*.hpp ${I_DIR}/*.h)
    SET(PLUGIN_HEADERS ${PLUGIN_HEADERS} ${IDIR_HEADERS})
  ENDFOREACH()

  # ====================================================================
  # add the install targets
  # headers are included here for IDE-based projects (VS, XCode, etc.)
  ADD_LIBRARY(${TargetName} SHARED ${PLUGIN_SRCS} ${PLUGIN_HEADERS})

  # ====================================================================
  # prepend include directories for header files
  TARGET_INCLUDE_DIRECTORIES(${TargetName} BEFORE PUBLIC ${PLUGIN_DIRS})

  # ====================================================================
  # Add source/header files to IDE-based project source groups
  # Macro defined in top-level CMakeLists.txt
  _ADDSOURCEGROUPS("${PLUGIN_DIRS}")

  # ====================================================================
  # Create build products in specified directory
  # Macro defined in top-level CMakeLists.txt
  _SETOUTPUTDIRECTORY(${TargetName} ${INSTALL_DIR})

  # ====================================================================
  # All plugins should start with "lib". This is always true for Mac/Linux, 
  # but needs to be specified for Windows.
  SET_TARGET_PROPERTIES(${TargetName} PROPERTIES PREFIX "lib")

  # ====================================================================
  # Plugin label should include "Plugin" in IDEs (e.g. VisualStudio)
  SET_TARGET_PROPERTIES(${TargetName} PROPERTIES PROJECT_LABEL "Plugin ${TargetName}")

  # ====================================================================
  # All plugins link to GmatUtil and GmatBase
  TARGET_LINK_LIBRARIES(${TargetName} PRIVATE GmatUtil)
  TARGET_LINK_LIBRARIES(${TargetName} PRIVATE GmatBase)

  # ====================================================================
  # Specify where to install plugin
  # - On Windows the dll is RUNTIME (lib is ARCHIVE, ignored here)
  # - On Mac/Linux the dylib/so is LIBRARY
  if(WIN32)
    INSTALL(TARGETS ${TargetName} RUNTIME DESTINATION ${INSTALL_DIR})
  else()
    if(APPLE AND NOT ("${ARGN}" STREQUAL "FORCE"))
      set(FULL_INSTALL_DIR ${GMAT_MAC_APPBUNDLE_PATH}/${INSTALL_DIR})
    else()
      set(FULL_INSTALL_DIR ${INSTALL_DIR})
    endif()
    INSTALL(TARGETS ${TargetName} LIBRARY DESTINATION ${FULL_INSTALL_DIR})
  endif()

ENDMACRO(_SETUPPLUGIN)

# ====================================================================
# Declare all public plugins and their default values
# FOR NEW PLUGINS: Add a new line here with the plugin name
#                  Insert new OPTION command alphabetically for readability
# OPTION(PLUGIN_<PLUGINNAME> "<PluginName> Plugin" ON|OFF)
OPTION(PLUGIN_CINTERFACE "CInterface Plugin" ON)
OPTION(PLUGIN_DATAINTERFACE "DataInterface Plugin" ON)
OPTION(PLUGIN_DATACALLBACK "DataCallback Plugin" OFF)
OPTION(PLUGIN_EPHEMPROPAGATOR "EphemPropagator Plugin" ON)
OPTION(PLUGIN_EKF "EKF Plugin" ON)
OPTION(PLUGIN_ESTIMATION "Estimation Plugins" ON)
OPTION(PLUGIN_EVENTLOCATOR "EventLocator Plugin" ON)
OPTION(PLUGIN_EXTRAPROPAGATORS "ExtraPropagators Plugin" ON)
OPTION(PLUGIN_FMINCONOPTIMIZER "FminconOptimizer Plugin" OFF)
OPTION(PLUGIN_FORMATION "Formation Plugin" ON)
OPTION(PLUGIN_GEOMETRICMEASUREMENT "GeometricMeasurement Plugin" OFF)
OPTION(PLUGIN_GMATFUNCTION "GmatFunction Plugin" ON)
OPTION(PLUGIN_MATLABINTERFACE "MatlabInterface Plugin" ON)
OPTION(PLUGIN_NEWPARAMETERS "NewParameter Plugin" ON)
OPTION(PLUGIN_POLYHEDRONGRAVITY "PolyhedronGravity Plugin" ON)
OPTION(PLUGIN_PRODUCTIONPROPAGATORS "ProductionPropagators Plugin" ON)
OPTION(PLUGIN_PYTHONINTERFACE "PythonInterface Plugin" ON)
OPTION(PLUGIN_SAVECOMMAND "SaveCommand Plugin" ON)
OPTION(PLUGIN_SCRIPTTOOLS "ScriptTools Plugin" ON)
OPTION(PLUGIN_STATION "Station Plugin" ON)
OPTION(PLUGIN_THRUSTFILE "ThrustFile Plugin" ON)
OPTION(PLUGIN_YUKONOPTIMIZER "YukonOptimizer Plugin" ON)

# Variable used to collect a list of all enabled plugins
SET(PluginTargets "")

# ====================================================================
# FOR NEW PLUGINS: This code adds your plugin to the build system
# Insert this code in alphabetical order between the plugins below
#if (PLUGIN_<PLUGINNAME>) # Use your plugin's name
#  SET(PLUGINDIR "<PluginDir>/src/base") # Use your plugin's directory name
#  ADD_SUBDIRECTORY(${PLUGINDIR}) 
#  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
#  SET(PluginTargets ${PluginTargets} ${tmp})
#endif()

# ====================================================================
# CInterface Plugin
if (PLUGIN_CINTERFACE)
  SET(PLUGINDIR "CInterfacePlugin/src")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# DataInterface Plugin
if (PLUGIN_DATAINTERFACE)
  SET(PLUGINDIR "DataInterfacePlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# DataCallback Plugin
if (PLUGIN_DATACALLBACK)
  SET(PLUGINDIR "DataCallbackPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# EphemPropagator Plugin
if (PLUGIN_EPHEMPROPAGATOR)
  SET(PLUGINDIR "EphemPropagatorPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# EKF Plugin
if (PLUGIN_EKF)
  SET(PLUGINDIR "ExtendedKalmanFilterPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# Estimation Plugin
if (PLUGIN_ESTIMATION)
  SET(PLUGINDIR "EstimationPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# EventLocator Plugin
if (PLUGIN_EVENTLOCATOR)
  SET(PLUGINDIR "EventLocatorPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# ExtraPropagators Plugin
if (PLUGIN_EXTRAPROPAGATORS)
  SET(PLUGINDIR "ExtraPropagatorsPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# FminconOptimizer Plugin
if (PLUGIN_FMINCONOPTIMIZER)
  SET(PLUGINDIR "FminconOptimizerPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# Formation Plugin
if (PLUGIN_FORMATION)
  SET(PLUGINDIR "FormationPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# GeometricMeasurement Plugin
if (PLUGIN_GEOMETRICMEASUREMENT)
  SET(PLUGINDIR "GeometricMeasurementPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# GmatFunction Plugin
if (PLUGIN_GMATFUNCTION)
  SET(PLUGINDIR "GmatFunctionPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# MatlabInterface Plugin
if (PLUGIN_MATLABINTERFACE)
  SET(PLUGINDIR "MatlabInterfacePlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# NewParameters Plugin
if (PLUGIN_NEWPARAMETERS)
  SET(PLUGINDIR "NewParameterPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# PolyhedronGravity Plugin
if (PLUGIN_POLYHEDRONGRAVITY)
  SET(PLUGINDIR "PolyhedronGravityPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# ProductionPropagators Plugin
if (PLUGIN_PRODUCTIONPROPAGATORS)
  SET(PLUGINDIR "ProductionPropagatorPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# PythonInterface Plugin
if (PLUGIN_PYTHONINTERFACE)
  SET(PLUGINDIR "PythonInterfacePlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# SaveCommand Plugin
if (PLUGIN_SAVECOMMAND)
  SET(PLUGINDIR "SaveCommandPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# ScriptTools Plugin
if (PLUGIN_SCRIPTTOOLS)
  SET(PLUGINDIR "ScriptToolsPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# Station Plugin
if (PLUGIN_STATION)
  SET(PLUGINDIR "StationPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

# ====================================================================
# ThrustFile Plugin
if (PLUGIN_THRUSTFILE)
  SET(PLUGINDIR "ThrustFilePlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()


# ====================================================================
# YukonOptimizer Plugin
if (PLUGIN_YUKONOPTIMIZER)
  SET(PLUGINDIR "YukonOptimizerPlugin/src/base")
  ADD_SUBDIRECTORY(${PLUGINDIR})
  GET_DIRECTORY_PROPERTY(tmp DIRECTORY ${PLUGINDIR} DEFINITION TargetName)
  SET(PluginTargets ${PluginTargets} ${tmp})
endif()

SET_TARGET_PROPERTIES(${PluginTargets} PROPERTIES FOLDER "GMAT Plugins")

# ====================================================================
# Proprietary plugins

# Search for proprietary plugin folder in default paths
# User can change this via CMake GUI or command line
FIND_PATH(GMAT_PROPRIETARYPLUGINS_PATH
  NAMES code/CMakeLists.txt
  PATHS
    ${PROJECT_SOURCE_DIR}/../gmatinternal-git
    ${PROJECT_SOURCE_DIR}/plugins/gmatinternal-git
  DOC "Path to GMAT proprietary plugins (containing code/)"
  NO_DEFAULT_PATH
  )

# Load CMakeLists.txt file from proprietary plugins /code folder
if(GMAT_PROPRIETARYPLUGINS_PATH)
  SET(PLUGINDIR "${GMAT_PROPRIETARYPLUGINS_PATH}/code")
  ADD_SUBDIRECTORY(${PLUGINDIR} "${CMAKE_CURRENT_BINARY_DIR}/proprietary")
  GET_DIRECTORY_PROPERTY(ProprietaryTargets DIRECTORY ${PLUGINDIR} DEFINITION PluginTargets)
  SET(PluginTargets ${PluginTargets} ${ProprietaryTargets})
  SET_TARGET_PROPERTIES(${ProprietaryTargets} PROPERTIES FOLDER "GMAT Plugins/Proprietary")
endif()

# ====================================================================
# Plugins from Other Sources
#
# Add Additional Vendor or Internal Plugins to the GMAT_EXTRA_PLUGINS file
find_file(GMAT_EXTRA_PLUGINS "ListOfPluginCollections.txt"
          DOC "Path to third party plugin list (containing group names, if any)")
if (GMAT_EXTRA_PLUGINS)
   FILE(READ ${GMAT_EXTRA_PLUGINS} OtherPluginSources)
   STRING(REGEX REPLACE ";" "\\\\;" OtherPluginSources "${OtherPluginSources}")
   STRING(REGEX REPLACE "\n" ";" OtherPluginSources "${OtherPluginSources}")

   foreach(folder ${OtherPluginSources})
      # Search for plugin folder in the folder containing the GMAT repo
      FIND_PATH(GMAT_ADDITIONAL_PLUGINS_${folder}
        NAMES CMakeLists.txt
        PATHS
          ${PROJECT_SOURCE_DIR}/../${folder}
        DOC "Path to additional plugin ${folder}"
        NO_DEFAULT_PATH
        )
   
      if(GMAT_ADDITIONAL_PLUGINS_${folder})
        SET(PLUGINDIR "${GMAT_ADDITIONAL_PLUGINS_${folder}}")
        ADD_SUBDIRECTORY(${PLUGINDIR} "${CMAKE_CURRENT_BINARY_DIR}/${folder}")
        GET_DIRECTORY_PROPERTY(ExtraTargets DIRECTORY ${PLUGINDIR} DEFINITION PluginTargets)
        SET_TARGET_PROPERTIES(${ExtraTargets} PROPERTIES FOLDER "GMAT Plugins/Extra")
        SET(PluginTargets ${PluginTargets} ${ExtraTargets})
      endif()
   endforeach()
endif()

# ====================================================================
# Set plugin properties all at once
if(PluginTargets)
  # Specify the library version
  SET_TARGET_PROPERTIES(${PluginTargets} PROPERTIES VERSION ${GMAT_VERSION})

  # Specify link flags for Win32 targets
  if(WIN32)
    SET_TARGET_PROPERTIES(${PluginTargets} PROPERTIES LINK_FLAGS "/NODEFAULTLIB:\"libcmt.lib\"")
  endif()
endif()
