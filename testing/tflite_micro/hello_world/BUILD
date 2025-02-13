#
# Copyright 2022 The Project Oak Authors
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

load(
    "//cc/tflite_micro:build_defs.bzl",
    "generate_cc_arrays",
    "oak_copts",
)

package(
    default_visibility = ["//visibility:public"],
    licenses = ["notice"],
)

generate_cc_arrays(
    name = "generated_hello_world_model_cc",
    src = "hello_world.tflite",
    out = "hello_world_model_data.cc",
)

generate_cc_arrays(
    name = "generated_hello_world_model_hdr",
    src = "hello_world.tflite",
    out = "hello_world_model_data.h",
)

cc_library(
    name = "hello_world_model",
    srcs = [
        ":generated_hello_world_model_cc",
    ],
    hdrs = [
        ":generated_hello_world_model_hdr",
    ],
)

# Can be used i.e. link to custom output handlers for testing
# where tensor outputs are checked and asserted.
config_setting(
    name = "use_custom_output",
    values = {
        "define": "use_custom_output=1",
    },
)

# Freestanding executable running on host for debugging
# i.e. model input, output, ops and pipeline via gdb/lldb.
cc_binary(
    name = "hello_world_freestanding_bin",
    srcs = ["main.cc"] + select({
        ":use_custom_output": [],
        "//conditions:default": [
            "output_handler.cc",
            "output_handler.h",
        ],
    }),
    copts = oak_copts() + [
        "-Itesting/tflite_micro/hello_world",
    ],
    linkopts = ["-nostdlib"],
    deps = [
        ":hello_world_model",
        "//cc/tflite_micro:hdrs",
        "//cc/tflite_micro:tflite_micro_generic",
        "//testing/tflite_micro:start_file",
    ],
)
