# Copyright 2017 The Bazel Authors. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

load("@bazel_gazelle//:def.bzl", "gazelle")
load(":version.bzl", "BAZEL_VERSION")

package(default_visibility = ["//visibility:public"])

licenses(["notice"])  # Apache 2.0

exports_files([
    "LICENSE",
    "version.bzl",
])

filegroup(
    name = "distribution",
    srcs = [
        "BUILD",
        "MODULE.bazel",
        "WORKSPACE",
        "internal_deps.bzl",
        "internal_setup.bzl",
        "//gazelle:distribution",
        "//python:distribution",
        "//python/pip_install:distribution",
        "//third_party/github.com/bazelbuild/bazel-skylib/lib:distribution",
        "//third_party/github.com/bazelbuild/bazel-skylib/rules:distribution",
        "//third_party/github.com/bazelbuild/bazel-skylib/rules/private:distribution",
        "//tools:distribution",
    ],
    visibility = [
        "//examples:__pkg__",
        "//python/tests/toolchains:__pkg__",
        "//tests:__pkg__",
    ],
)

# Reexport of all bzl files used to allow downstream rules to generate docs
# without shipping with a dependency on Skylib
filegroup(
    name = "bzl",
    srcs = [
        "//python/pip_install:bzl",
        "//python:bzl",
        # Requires Bazel 0.29 onward for public visibility of these .bzl files.
        "@bazel_tools//tools/python:private/defs.bzl",
        "@bazel_tools//tools/python:python_version.bzl",
        "@bazel_tools//tools/python:srcs_version.bzl",
        "@bazel_tools//tools/python:toolchain.bzl",
        "@bazel_tools//tools/python:utils.bzl",
    ],
    visibility = ["//visibility:public"],
)

# Gazelle configuration options.
# See https://github.com/bazelbuild/bazel-gazelle#running-gazelle-with-bazel
# gazelle:prefix github.com/bazelbuild/rules_python
# gazelle:exclude bazel-out
# gazelle:exclude examples/**
gazelle(name = "gazelle")

gazelle(
    name = "gazelle_update_repos",
    args = [
        "-from_file=go.mod",
        "-to_macro=gazelle/deps.bzl%gazelle_deps",
        "-prune",
    ],
    command = "update-repos",
)

genrule(
    name = "assert_bazelversion",
    srcs = [".bazelversion"],
    outs = ["assert_bazelversion_test.sh"],
    cmd = """\
set -o errexit -o nounset -o pipefail
current=$$(cat "$(execpath .bazelversion)")
cat > "$@" <<EOF
#!/usr/bin/env bash
set -o errexit -o nounset -o pipefail
if [[ \"$${{current}}\" != \"{expected}\" ]]; then
    >&2 echo "ERROR: current bazel version '$${{current}}' is not the expected '{expected}'"
    exit 1
fi
EOF
""".format(
        expected = BAZEL_VERSION,
    ),
    executable = True,
)

sh_test(
    name = "assert_bazelversion_test",
    srcs = [":assert_bazelversion_test.sh"],
)
