# cmake needs this line
cmake_minimum_required(VERSION 3.1)

# Enable C++11
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

# Define project name
project(centernet_detection)

SET (CMAKE_VERBOSE_MAKEFILE 1)

# Compile options
set(CMAKE_C_FLAGS "-Wall -pthread ")
set(CMAKE_C_FLAGS_DEBUG "-g -O0")
set(CMAKE_C_FLAGS_RELEASE "-O3")
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -lstdc++")
set(CMAKE_CXX_FLAGS_DEBUG ${CMAKE_C_FLAGS_DEBUG})
set(CMAKE_CXX_FLAGS_RELEASE ${CMAKE_C_FLAGS_RELEASE})

set(CMAKE_BUILD_TYPE release)
# set(CMAKE_BUILD_TYPE debug)

# Find OpenCV, you may need to set OpenCV_DIR variable
# to the absolute path to the directory containing OpenCVConfig.cmake file
# via the command line or GUI
find_package(OpenCV REQUIRED)

find_package(Boost REQUIRED COMPONENTS filesystem system)

# set(THREADS_PREFER_PTHREAD_FLAG ON)
#find_package(Threads REQUIRED)

# If the package has been found, several variables will
# be set, you can find the full list with descriptions
# in the OpenCVConfig.cmake file.
# Print some message showing some of them
message(STATUS "OpenCV library status:")
message(STATUS "    config: ${OpenCV_DIR}")
message(STATUS "    version: ${OpenCV_VERSION}")
message(STATUS "    libraries: ${OpenCV_LIBS}")
message(STATUS "    include path: ${OpenCV_INCLUDE_DIRS}")

# Declare the executable target built from your sources
add_executable(centernet_detection main.cpp centernet_detector.cpp)

# Link your application with OpenCV libraries
target_link_libraries(centernet_detection LINK_PRIVATE ${OpenCV_LIBS})

# Link your application with OpenCV libraries
target_link_libraries(centernet_detection LINK_PRIVATE Boost::filesystem)

EXECUTE_PROCESS(COMMAND uname -m COMMAND tr -d '\n' OUTPUT_VARIABLE ARCHITECTURE)
message(STATUS "Architecture: ${ARCHITECTURE}")

# Link your application with Tensorflow Lite
target_link_libraries(centernet_detection LINK_PRIVATE ${CMAKE_SOURCE_DIR}/../../tensorflow/bazel-bin/tensorflow/lite/libtensorflowlite.so)


# target_link_libraries(centernet_detection Threads::Threads)

find_library(LIBRT rt)
if(LIBRT)
  target_link_libraries(centernet_detection LINK_PRIVATE ${LIBRT})
endif()

find_library(LIBDL dl)
if(LIBDL)
  target_link_libraries(centernet_detection LINK_PRIVATE ${LIBDL})
endif()

target_include_directories(centernet_detection PUBLIC ${CMAKE_SOURCE_DIR})
target_include_directories(centernet_detection PUBLIC ${CMAKE_SOURCE_DIR}/../../tensorflow/)
target_include_directories(centernet_detection PUBLIC ${CMAKE_SOURCE_DIR}/../../tensorflow/tensorflow/lite/tools/make/downloads/flatbuffers/include)
