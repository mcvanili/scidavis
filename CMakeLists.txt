cmake_minimum_required(VERSION 3.16)

project(scidavis
  VERSION 3.0.0
  DESCRIPTION "SciDAVis is a free application for Scientific Data Analysis and Visualization."
  HOMEPAGE_URL "https://scidavis.sourceforge.net"
  LANGUAGES CXX C)

# Qt6 is required
find_package(Qt6
  COMPONENTS
  Core
  Gui
  Widgets
  Svg
  PrintSupport
  Xml
  OpenGL
  LinguistTools
  REQUIRED
)

# Python is required
find_package(Python3 COMPONENTS Interpreter Development REQUIRED)

# Check for PyQt6
execute_process(
  COMMAND ${Python3_EXECUTABLE} -c "import PyQt6"
  RESULT_VARIABLE PYQT6_FOUND
  OUTPUT_QUIET
  ERROR_QUIET
)
if(NOT PYQT6_FOUND EQUAL 0)
  message(FATAL_ERROR "PyQt6 is required but was not found in Python environment")
endif()

# Check for matplotlib with Qt6 backend support
execute_process(
  COMMAND ${Python3_EXECUTABLE} -c "
import matplotlib
matplotlib.use('Qt6Agg')
from matplotlib.backends.backend_qt6agg import FigureCanvasQTAgg
"
  RESULT_VARIABLE MATPLOTLIB_QT6_FOUND
  OUTPUT_QUIET
  ERROR_QUIET
)
if(NOT MATPLOTLIB_QT6_FOUND EQUAL 0)
  message(FATAL_ERROR "matplotlib with Qt6 backend support is required but was not found.\nPlease install: pip install matplotlib PyQt6")
endif()

message(STATUS "Found matplotlib with Qt6 backend support")

# Core dependencies
find_package(GSL REQUIRED)
find_package(muparser REQUIRED)

# Set up Python scripting
set(SCRIPTING_PYTHON ON)
add_definitions(-DSCRIPTING_PYTHON)

# Include directories
include_directories(
  ${CMAKE_SOURCE_DIR}/libscidavis/src
  ${Python3_INCLUDE_DIRS}
  ${GSL_INCLUDE_DIRS}
)