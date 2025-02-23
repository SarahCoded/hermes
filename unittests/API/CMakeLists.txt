# Copyright (c) Facebook, Inc. and its affiliates.
#
# This source code is licensed under the MIT license found in the
# LICENSE file in the root directory of this source tree.

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(APITestsSources
  APITest.cpp
  APITestFactory.cpp
  DebuggerTest.cpp
  SegmentTest.cpp
  )
set(APISegmentTestCompileSources
  SegmentTestCompile.cpp
  )

set(HERMES_LINK_COMPONENTS LLVHSupport)

# Build SegmentTestCompile without EH and RTTI
add_hermes_library(SegmentTestCompile STATIC ${APISegmentTestCompileSources})

# Turn on EH and RTTI for APITests
set(HERMES_ENABLE_EH ON)
set(HERMES_ENABLE_RTTI ON)
# For some reason (bug?) add_unittest() is clearing LLVM_REQUIRES_RTTI, so
# we need to set this one.
set(LLVM_ENABLE_RTTI ON)

add_hermes_unittest(APITests ${APITestsSources})

# TODO: The synth trace and heap snapshot tests are currently disabled
# because of build issues. We should find a way to re-enable them.
target_link_libraries(APITests hermesapi compileJS SegmentTestCompile)

add_hermes_unittest(APILeanTests APILeanTest.cpp)
target_link_libraries(APILeanTests hermesapiLean)
