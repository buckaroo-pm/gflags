load('//:buckaroo_macros.bzl', 'buckaroo_deps_from_package')
load('//:subdir_glob.bzl', 'subdir_glob')

namespace = read_config('gflags', 'namespace', 'google')

gflags_ns_h_files = []

genrule(
  name = 'gflags_declare-h',
  out = 'gflags_declare.h',
  srcs = [
    'src/gflags_declare.h.in',
  ],
  cmd  = ("awk '{ " +
          "gsub(/@GFLAGS_NAMESPACE@/, \"" + namespace + "\"); " +
          "gsub(/@(HAVE_STDINT_H|HAVE_SYS_TYPES_H|HAVE_INTTYPES_H|GFLAGS_INTTYPES_FORMAT_C99)@/, \"1\"); " +
          "gsub(/@([A-Z0-9_]+)@/, \"0\"); " +
          "print; }' $SRCS > $OUT")
)

genrule(
  name = 'gflags-h',
  out = 'gflags.h',
  srcs = [
    'src/gflags.h.in',
  ],
  cmd  = ("awk '{ " +
          "gsub(/@GFLAGS_ATTRIBUTE_UNUSED@/, \"\"); " +
          "gsub(/@INCLUDE_GFLAGS_NS_H@/, \"" + '\n'.join(["#include \\\"gflags/{}\\\"".format(hdr) for hdr in gflags_ns_h_files]) + "\"); " +
          "print; }' $SRCS > $OUT")
)

genrule(
  name = 'gflags_completions-h',
  out = 'gflags_completions.h',
  srcs = [
    'src/gflags_completions.h.in',
  ],
  cmd  = "awk '{ gsub(/@GFLAGS_NAMESPACE@/, \"" + namespace + "\"); print; }' $SRCS > $OUT"
)

cxx_library(
  name = 'gflags',
  header_namespace = '',
  exported_headers = {
    'gflags/gflags_declare.h': ':gflags_declare-h',
    'gflags/gflags.h': ':gflags-h',
    'gflags/gflags_completions.h': ':gflags_completions-h',
  },
  headers = subdir_glob([
    ('src', '**/*.h'),
  ]),
  srcs = glob([
    'src/**/gflags*.cc',
  ]),
  platform_srcs = [
    ('window.*', glob([ 'src/**/windows*.cc' ])),
  ],
  preprocessor_flags = [
    '-DGOOGLE_NAMESPACE=' + namespace,
  ],
  platform_deps = [
    ('linux.*', buckaroo_deps_from_package('github.com/buckaroo-pm/host-pthread')),
  ],
  visibility = [
    'PUBLIC',
  ],
)
