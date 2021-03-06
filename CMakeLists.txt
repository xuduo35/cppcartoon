cmake_minimum_required(VERSION 2.8)
set(CMAKE_MACOSX_RPATH 0)
project(cartoon)

GET_FILENAME_COMPONENT(CMAKE_MODULE_DIR ${CMAKE_CURRENT_SOURCE_DIR} DIRECTORY)
SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_DIR}/cmake_modules)
MESSAGE(STATUS "CMAKE_MODULE_PATH: ${CMAKE_MODULE_PATH}")

SET(CMAKE_CXX_FLAGS_DEBUG "-std=c++11 -O0 -Wall -g -ggdb")
SET(CMAKE_CXX_FLAGS_RELEASE "-std=c++11 -O2 -Wall -DDLIB_HAVE_AVX2 -mavx2")

MESSAGE(STATUS "CMAKE_CXX_FLAGS:${CMAKE_CXX_FLAGS}")
MESSAGE(STATUS "CMAKE_CXX_FLAGS_RELEASE:${CMAKE_CXX_FLAGS_RELEASE}")

find_package(OpenCV REQUIRED)
MESSAGE(STATUS "OpenCV_INCLUDE_DIRECTORIES:${OpenCV_INCLUDE_DIRECTORIES}")

FIND_PACKAGE(PythonLibs 2.7 REQUIRED)
IF(PYTHONLIBS_FOUND)
    INCLUDE_DIRECTORIES(${PYTHON_INCLUDE_DIRS})
    MESSAGE(STATUS "PYTHON_INCLUDE_DIRS:${PYTHON_INCLUDE_DIRS}")
ENDIF(PYTHONLIBS_FOUND)

#FIND_PACKAGE(NumPy)
#IF(PYTHON_NUMPY_FOUND)
#   INCLUDE_DIRECTORIES(${PYTHON_NUMPY_INCLUDE_DIR})
#ENDIF(PYTHON_NUMPY_FOUND)

EXECUTE_PROCESS(COMMAND ../FindNumpy.py OUTPUT_VARIABLE PYTHON_NUMPY_INCLUDE_DIR RESULT_VARIABLE CMD_ERROR)
MESSAGE( STATUS "CMD_ERROR:" ${CMD_ERROR})
MESSAGE( STATUS "CMD_OUTPUT:" ${PYTHON_NUMPY_INCLUDE_DIR})

INCLUDE_DIRECTORIES(${PYTHON_NUMPY_INCLUDE_DIR})
INCLUDE_DIRECTORIES(include)

FIND_PACKAGE(Boost)
if(Boost_FOUND)
    LINK_DIRECTORIES(${Boost_LIBRARY_DIRS})
    MESSAGE( STATUS "Boost_LIBRARY_DIRS:" ${Boost_LIBRARY_DIRS})
endif()

# Library
AUX_SOURCE_DIRECTORY(src SRC)
#ADD_LIBRARY(cartoon SHARED ${SRC})

# Python
AUX_SOURCE_DIRECTORY(src/python SRC_PYTHON)
ADD_LIBRARY(pycartoon SHARED ${SRC} ${SRC_PYTHON})
SET_TARGET_PROPERTIES(pycartoon PROPERTIES PREFIX "" OUTPUT_NAME "pycartoon" SUFFIX ".so")
TARGET_LINK_LIBRARIES(pycartoon
    ${PYTHON_LIBRARIES} boost_python opencv_videoio opencv_imgcodecs opencv_imgproc)
