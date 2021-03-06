#  Copyright (c) 2014-present, Facebook, Inc.
#  All rights reserved.
#
#  This source code is licensed under the BSD-style license found in the
#  LICENSE file in the root directory of this source tree. An additional grant
#  of patent rights can be found in the PATENTS file in the same directory.

# Generate the thrift intermediate/interface code.
if(LINUX OR DARWIN)
  add_custom_command(
    COMMAND
      LD_LIBRARY_PATH=${BUILD_DEPS}/lib:$ENV{LD_LIBRARY_PATH}
      ${THRIFT_COMPILER} --gen cpp --gen py "${CMAKE_SOURCE_DIR}/osquery.thrift"
    DEPENDS "${CMAKE_SOURCE_DIR}/osquery.thrift"
    WORKING_DIRECTORY "${CMAKE_BINARY_DIR}/generated"
    OUTPUT ${OSQUERY_THRIFT_GENERATED_FILES}
  )
else()
  add_custom_command(
    COMMAND
      ${THRIFT_COMPILER} --gen cpp --gen py "${CMAKE_SOURCE_DIR}/osquery.thrift"
    DEPENDS "${CMAKE_SOURCE_DIR}/osquery.thrift"
    WORKING_DIRECTORY "${CMAKE_BINARY_DIR}/generated"
    OUTPUT ${OSQUERY_THRIFT_GENERATED_FILES}
  )
endif()

if(NOT WINDOWS)
  add_compile_options(
    -Wno-macro-redefined
  )
endif()

ADD_OSQUERY_LIBRARY(TRUE osquery_extensions
  ${OSQUERY_THRIFT_GENERATED_FILES}
  extensions.cpp
  interface.cpp
)

file(GLOB OSQUERY_EXTENSIONS_TESTS "tests/*.cpp")
ADD_OSQUERY_TEST(TRUE ${OSQUERY_EXTENSIONS_TESTS})
