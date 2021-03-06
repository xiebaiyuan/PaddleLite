lite_cc_library(subgraph_detector
    SRCS subgraph_detector.cc
    DEPS mir_pass types subgraph_op)
lite_cc_library(subgraph_pass
    SRCS subgraph_pass.cc
    DEPS mir_pass types context ${mir_fusers} subgraph_detector)
if (WITH_TESTING)
    lite_cc_test(test_subgraph_detector
        SRCS subgraph_detector_test.cc
        DEPS subgraph_detector mir_passes gflags model_parser cxx_api
        ARGS --model_dir=${LITE_MODEL_DIR}/mobilenet_v1 SERIAL)
    add_dependencies(test_subgraph_detector
        extern_lite_download_mobilenet_v1_tar_gz
        extern_lite_download_mobilenet_v2_relu_tar_gz)
    set(LINK_FLAGS "-Wl,--version-script ${PADDLE_SOURCE_DIR}/lite/core/lite.map")
    set_target_properties(test_subgraph_detector PROPERTIES LINK_FLAGS "${LINK_FLAGS}")
    lite_cc_test(test_subgraph_pass
        SRCS subgraph_pass_test.cc
        DEPS mir_passes paddle_api_full paddle_api_light gflags
        ARGS --model_dir=${LITE_MODEL_DIR}/mobilenet_v1
             --optimized_model_dir=${LITE_MODEL_DIR}/lite_model_opt SERIAL)
    add_dependencies(test_subgraph_pass
        extern_lite_download_mobilenet_v1_tar_gz
        extern_lite_download_mobilenet_v2_relu_tar_gz)
    set(LINK_FLAGS "-Wl,--version-script ${PADDLE_SOURCE_DIR}/lite/core/lite.map")
    set_target_properties(test_subgraph_pass PROPERTIES LINK_FLAGS "${LINK_FLAGS}")
endif()

set(mir_subgraphs subgraph_pass CACHE INTERNAL "mir_subgraphs")
message(STATUS "----> mir_subgraphs: ${mir_subgraphs}")
