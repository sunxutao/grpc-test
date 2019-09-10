cmake_minimum_required(VERSION 3.12)
project(grpc-test)

# configuration
cmake_policy(SET CMP0074 NEW)  # enable <PackageName>_ROOT environment variable
set(CMAKE_MODULE_PATH 
	"${CMAKE_CURRENT_SOURCE_DIR}/cmake" 
	"${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules" 
	"${CMAKE_MODULE_PATH}"
)

option(ROUTE  "Compile route guide case" OFF)

set(INCLUDE_DIRS "${PROJECT_SOURCE_DIR}/include")

# set up the install path of thirdparty packages
set(INSTALL_PATH "$ENV{HOME}/local" CACHE PATH "install path of thirdparty modules")

# set up the root path of thirdparty packages
set(Protobuf_ROOT "${INSTALL_PATH}/protobuf" CACHE PATH "root path of Protobuf")
set(Grpc_ROOT "${INSTALL_PATH}/grpc" CACHE PATH "root path of Gprc")

# dependencies
include(FindRequiredPackage)

#---[ protobuf
FindRequiredPackage(NAME Protobuf)
if (Protobuf_PROTOC_EXECUTABLE)
  set(_PROTOBUF_PROTOC $<TARGET_FILE:protobuf::protoc>)
else ()
  set(_PROTOBUF_PROTOC "${Protobuf_ROOT}/bin/protoc")
endif ()

#---[ grpc
FindRequiredPackage(NAME Grpc)
if (Grpc_EXECUTABLES)
  set(_GRPC_CPP_PLUGIN_EXECUTABLE $<TARGET_FILE:grpc::grpc_cpp_plugin>)
else ()
  set(_GRPC_CPP_PLUGIN_EXECUTABLE "${Grpc_ROOT}/bin/grpc_cpp_plugin")
endif ()

# compilation
set(CMAKE_CXX_FLAGS 
  "${CMAKE_CXX_FLAGS} -std=c++11 -O2 -pthread -Wall -Wno-unused-function")

# Include generated *.pb.h files
include_directories(${INCLUDE_DIRS})

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

add_subdirectory(src)