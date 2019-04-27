add_library(handler STATIC
    "crash_report_upload_thread.cc"
    "crash_report_upload_thread.h"
    "handler_main.cc"
    "handler_main.h"
    "minidump_to_upload_parameters.cc"
    "minidump_to_upload_parameters.h"
    "prune_crash_reports_thread.cc"
    "prune_crash_reports_thread.h"
    "user_stream_data_source.cc"
    "user_stream_data_source.h"
)
target_link_libraries(handler
    PUBLIC client base util
    PRIVATE minidump snapshot tool_support
)
target_compile_definitions(handler PRIVATE "WIN32_LEAN_AND_MEAN")

if(APPLE)
    target_sources(handler PRIVATE
        "mac/crash_report_exception_handler.cc"
        "mac/crash_report_exception_handler.h"
        "mac/exception_handler_server.cc"
        "mac/exception_handler_server.h"
        "mac/file_limit_annotation.cc"
        "mac/file_limit_annotation.h"
    )
endif()

if(UNIX OR ANDROID)
    target_sources(handler PRIVATE
        "linux/crash_report_exception_handler.cc"
        "linux/crash_report_exception_handler.h"
        "linux/exception_handler_server.cc"
        "linux/exception_handler_server.h"
    )
endif()

if(WIN32)
    target_sources(handler PRIVATE
        "win/crash_report_exception_handler.cc"
        "win/crash_report_exception_handler.h"
    )
    target_compile_options(handler PRIVATE "/wd4201")
endif()

add_executable(crashpad_handler WIN32 "main.cc")
target_link_libraries(crashpad_handler PRIVATE handler compat base)
install(TARGETS crashpad_handler)
