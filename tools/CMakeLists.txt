add_library(tool_support STATIC
    "tool_support.cc"
    "tool_support.h"
)
target_include_directories(tool_support PUBLIC "..")
target_link_libraries(tool_support PRIVATE base)

add_executable(crashpad_database_util "crashpad_database_util.cc")
target_link_libraries(crashpad_database_util PRIVATE
    tool_support
    client
    compat
    base
    util
)

add_executable(crashpad_http_upload "crashpad_http_upload.cc")
target_link_libraries(crashpad_http_upload PRIVATE
    tool_support
    compat
    base
    util
)

add_executable(generate_dump "generate_dump.cc")
target_link_libraries(generate_dump PRIVATE
    tool_support
    minidump
    snapshot
    compat
    base
    util
)
if(WIN32)
    target_compile_options(generate_dump PRIVATE "/wd4201")     # nonstandard extension used : nameless struct/union
endif()
