load('@bazel_tools//tools/build_defs/repo:git.bzl', system_git_repository='git_repository')

local_repository(
    name = "build_bazel_rules_swift",
    path = "/Users/rmalik/dev/rules_swift"
#    remote = "https://github.com/bazelbuild/rules_swift.git",
#    tag = "0.3.0",
)

load(
    "@build_bazel_rules_swift//swift:repositories.bzl",
    "swift_rules_dependencies",
)

swift_rules_dependencies()
