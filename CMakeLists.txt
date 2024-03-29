cmake_minimum_required(VERSION 3.18)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_COVERAGE "Build coverage" OFF)

set(
  HUNTER_CACHE_SERVERS
  "https://github.com/bmstu-iu8-cpp-sem-3/hunter-cache"
  CACHE STRING "Default cache server"
)

include("cmake/HunterGate.cmake")

HunterGate(
  URL "https://github.com/cpp-pm/hunter/archive/v0.23.314.tar.gz"
  SHA1 "95c47c92f68edb091b5d6d18924baabe02a6962a"
)

project(echoservice)
string(APPEND CMAKE_CXX_FLAGS " -pedantic -Werror -Wall -Wextra")
string(APPEND CMAKE_CXX_FLAGS " -Wno-unused-command-line-argument -Wno-unused-parameter")
string(APPEND CMAKE_CXX_FLAGS " -Wshadow -Wnon-virtual-dtor")

# new versions of cmake contain gtest search files
# commented out to increase the build speed.
# hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)

hunter_add_package(gRPC)
find_package(gRPC CONFIG REQUIRED)

set(API_GENERATED_DIR ${CMAKE_CURRENT_SOURCE_DIR}/third-party/echo/ccpkg)

set(PROTO_GENERATED_SOURCES
  "${API_GENERATED_DIR}/echo.pb.cc"
  "${API_GENERATED_DIR}/google/api/annotations.pb.cc"
  "${API_GENERATED_DIR}/google/api/http.pb.cc"
  "${API_GENERATED_DIR}/google/rpc/status.pb.cc")

set(PROTO_GENERATED_HEADERS
  "${API_GENERATED_DIR}/echo.pb.h"
  "${API_GENERATED_DIR}/google/api/annotations.pb.h"
  "${API_GENERATED_DIR}/google/api/http.pb.h"
  "${API_GENERATED_DIR}/google/rpc/status.pb.h")

set(PROTO_GENERATED_GRPC_SOURCES
  "${API_GENERATED_DIR}/echo.grpc.pb.cc"
  "${API_GENERATED_DIR}/google/api/annotations.grpc.pb.cc"
  "${API_GENERATED_DIR}/google/api/http.grpc.pb.cc"
  "${API_GENERATED_DIR}/google/rpc/status.grpc.pb.cc")

set(PROTO_GENERATED_GRPC_HEADERS
  "${API_GENERATED_DIR}/echo.grpc.pb.h"
  "${API_GENERATED_DIR}/google/api/annotations.grpc.pb.h"
  "${API_GENERATED_DIR}/google/api/http.grpc.pb.h"
  "${API_GENERATED_DIR}/google/rpc/status.grpc.pb.h")

add_executable(grpc_client
  ${CMAKE_CURRENT_SOURCE_DIR}/sources/main.cpp
  ${PROTO_GENERATED_SOURCES}
  ${PROTO_GENERATED_GRPC_SOURCES})

target_include_directories(grpc_client PRIVATE "${API_GENERATED_DIR}")

target_link_libraries(grpc_client
  gRPC::grpc++ gRPC::grpc++_reflection)
