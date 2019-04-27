set(srcs
    "zlib/adler32.c"
    "zlib/compress.c"
    "zlib/crc32.c"
    "zlib/crc32.h"
    "zlib/deflate.c"
    "zlib/deflate.h"
    "zlib/gzclose.c"
    "zlib/gzguts.h"
    "zlib/gzlib.c"
    "zlib/gzread.c"
    "zlib/gzwrite.c"
    "zlib/infback.c"
    "zlib/inffast.c"
    "zlib/inffast.h"
    "zlib/inffixed.h"
    "zlib/inflate.c"
    "zlib/inflate.h"
    "zlib/inftrees.c"
    "zlib/inftrees.h"
    "zlib/names.h"
    "zlib/trees.c"
    "zlib/trees.h"
    "zlib/uncompr.c"
    "zlib/zconf.h"
    "zlib/zlib.h"
    #"zlib/zlib_crashpad.h"
    "zlib_crashpad.h"
    "zlib/zutil.c"
    "zlib/zutil.h"
)
add_library(zlib STATIC ${srcs})
target_include_directories(zlib PUBLIC "zlib")
target_compile_definitions(zlib PRIVATE "HAVE_STDARG_H" PUBLIC "CRASHPAD_ZLIB_SOURCE_EMBEDDED")

if(WIN32)
    target_compile_options(zlib PRIVATE
        "/wd4131"  # uses old-style declarator
        "/wd4244"  # conversion from 't1' to 't2' possible loss of data
        "/wd4245"  # conversion from 't1' to 't2' signed/unsigned mismatch
        "/wd4267"  # conversion from 'size_t' to 't' possible loss of data
        "/wd4324"  # structure was padded due to alignment specifier
        "/wd4702"  # unreachable code
    )
else()
    target_compile_options(zlib PRIVATE
        "HAVE_HIDDEN"
        "HAVE_UNISTD_H"
    )
endif()

if(PROJECT_TARGET_ARCH STREQUAL "x86" OR PROJECT_TARGET_ARCH STREQUAL "x86_64")
    target_sources(zlib PRIVATE
        "zlib/crc_folding.c"
        "zlib/fill_window_sse.c"
        "zlib/x86.c"
        "zlib/x86.h"
    )
else()
    target_sources(zlib PRIVATE "zlib/simd_stub.c")
endif()
