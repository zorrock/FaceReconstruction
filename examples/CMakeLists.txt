project(examples)
cmake_minimum_required(VERSION 2.8.10)

# The examples need a few additional dependencies (e.g. boost filesystem and OpenCV highgui):

#check installed version in order to include the correct OpenCV libraries
#version variable is defined from project root's CMakeLists
if("${OpenCV_VERSION_MAJOR}$" EQUAL 2)
  message(STATUS "OpenCV 2.x detected")
  find_package(OpenCV 2.4.3 REQUIRED core imgproc highgui)
elseif("${OpenCV_VERSION_MAJOR}$" EQUAL 3)
  message(STATUS "OpenCV 3.x detected - including imgcodecs for compatibility")
  find_package(OpenCV 3.0.0 REQUIRED core imgproc imgcodecs)
endif()
message(STATUS "OpenCV include dir found at ${OpenCV_INCLUDE_DIRS}")
message(STATUS "OpenCV lib dir found at ${OpenCV_LIB_DIR}")
# This allows us to compile in RelWithDebInfo. It'll use the Release-version of OpenCV:
set_target_properties(${OpenCV_LIBS} PROPERTIES MAP_IMPORTED_CONFIG_RELWITHDEBINFO RELEASE)

find_package(Boost 1.50.0 COMPONENTS system filesystem program_options REQUIRED)

if(Boost_FOUND)
  message(STATUS "Boost found at ${Boost_INCLUDE_DIRS}")
else(Boost_FOUND)
  message(FATAL_ERROR "Boost not found")
endif()

# Keegan.Ren
#### dlib-begin
include(../3rdparty/dlib-18.17/dlib/cmake)

ADD_EXECUTABLE(face_landmark_detection_ex face_landmark_detection_ex.cpp)
TARGET_LINK_LIBRARIES(face_landmark_detection_ex dlib)

find_package(OpenCV)
if (OpenCV_FOUND)
   include_directories(${OpenCV_INCLUDE_DIRS})

   ADD_EXECUTABLE(webcam_face_pose_ex webcam_face_pose_ex.cpp)
   TARGET_LINK_LIBRARIES(webcam_face_pose_ex dlib ${OpenCV_LIBS} )
endif()

if (OpenCV_FOUND)
   include_directories(${OpenCV_INCLUDE_DIRS})
   
   ADD_EXECUTABLE(webcam_face_fit_model_keegan webcam_face_fit_model_keegan.cpp)
   TARGET_LINK_LIBRARIES(webcam_face_fit_model_keegan dlib ${OpenCV_LIBS} ${Boost_LIBRARIES})
endif()

# Model fitting (affine cam & shape to landmarks) example:
if (OpenCV_FOUND)
   include_directories(${OpenCV_INCLUDE_DIRS})
   
   add_executable(fit-model fit-model.cpp)
   target_link_libraries(fit-model ${OpenCV_LIBS} ${Boost_LIBRARIES})
endif()

# Converts a CVSSP .scm Morphable Model to a cereal binary file:
if (OpenCV_FOUND)
   include_directories(${OpenCV_INCLUDE_DIRS})
   
   add_executable(scm-to-cereal scm-to-cereal.cpp)
   target_link_libraries(scm-to-cereal ${OpenCV_LIBS} ${Boost_LIBRARIES})
endif()

#### dlib-end

# install target:
install(TARGETS fit-model DESTINATION bin)
install(TARGETS scm-to-cereal DESTINATION bin)
install(DIRECTORY ${CMAKE_SOURCE_DIR}/examples/data DESTINATION bin)
