# Stage 5

外部的项目同样适用两个库，一个是Bazel项目的abseil，另外一个是non-Bazel的zlib。

WORKSPACE文件

```python
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "com_google_absl",
    sha256 = "84b4277a9b56f9a192952beca535313497826c6ff2e38b2cac7351a3ed2ae780",
    strip_prefix = "abseil-cpp-c476da141ca9cffc2137baf85872f0cae9ffa9ad",
    url = "https://github.com/abseil/abseil-cpp/archive/c476da141ca9cffc2137baf85872f0cae9ffa9ad.zip",
)

http_archive(
    name = "zlib",
    #build_file = "//:zlib.BUILD.bazel",
    build_file_content = """
cc_library(
    name = "zlib",
    srcs = glob(["*.c", "*.h"]),
    includes = ["."],
    visibility = ["//visibility:public"],
)
    """,
    sha256 = "c3e5e9fdd5004dcb542feda5ee4f0ff0744628baf8ed2dd5d66f8ca1197cb1a1",
    strip_prefix = "zlib-1.2.11",
    urls = ["https://mirror.bazel.build/zlib.net/zlib-1.2.11.tar.gz"],
)
```



有两个简单的建议：

> Prefer [`http_archive`](https://docs.bazel.build/versions/master/repo/http.html#http_archive) to `git_repository` and `new_git_repository`.
>
> When using a `new_` repository rule, prefer to specify `build_file_content`, not `build_file`.

上边的WORKSPACE文件中有几个常用的要点:

- load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive") Stage 6中有介绍

- http_archive
- build_file_content
- glob
- strip_prefix

再看下main/BUILD文件比较简单：

```
cc_binary(
    name = "hello-world",
    srcs = ["hello-world.cc"],
    deps = [
        "@com_google_absl//absl/strings",
        "@zlib",
    ],
)
```