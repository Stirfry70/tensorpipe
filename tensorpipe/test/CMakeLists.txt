# Copyright (c) Facebook, Inc. and its affiliates.
# All rights reserved.
#
# This source code is licensed under the BSD-style license found in the
# LICENSE file in the root directory of this source tree.

add_executable(tensorpipe_test
  test.cc
  transport/context_test.cc
  transport/connection_test.cc
  transport/uv/uv_test.cc
  transport/uv/context_test.cc
  transport/uv/loop_test.cc
  transport/uv/connection_test.cc
  transport/uv/sockaddr_test.cc
  transport/listener_test.cc
  core/context_test.cc
  channel/basic/basic_test.cc
  channel/xth/xth_test.cc
  channel/mpt/mpt_test.cc
  channel/channel_test.cc
  common/system_test.cc
  common/defs_test.cc
  )

if(TP_ENABLE_SHM)
  target_sources(tensorpipe_test PRIVATE
    transport/shm/reactor_test.cc
    transport/shm/loop_test.cc
    transport/shm/connection_test.cc
    transport/shm/sockaddr_test.cc
    transport/shm/shm_test.cc
    util/ringbuffer/shm_ringbuffer_test.cc
    util/ringbuffer/ringbuffer_test.cc
    util/shm/segment_test.cc
    )
endif()

if(TP_ENABLE_CMA)
  target_sources(tensorpipe_test PRIVATE
    channel/cma/cma_test.cc
    )
endif()

if(TP_ENABLE_CUDA_IPC)
  find_package(CUDA REQUIRED)
  target_link_libraries(tensorpipe_test PRIVATE ${CUDA_LIBRARIES})
  target_include_directories(tensorpipe_test PRIVATE ${CUDA_INCLUDE_DIRS})

  target_sources(tensorpipe_test PRIVATE
    channel/cuda_ipc/cuda_ipc_test.cc
    )
  cuda_add_library(tensorpipe_cuda_ipc_kernel channel/cuda_ipc/kernel.cu)
  target_link_libraries(tensorpipe_test PRIVATE tensorpipe_cuda_ipc_kernel)
endif()


add_subdirectory(${PROJECT_SOURCE_DIR}/third_party/googletest
  ${PROJECT_BINARY_DIR}/third_party/googletest)

target_link_libraries(tensorpipe_test PRIVATE
  tensorpipe
  uv::uv
  gtest_main)
