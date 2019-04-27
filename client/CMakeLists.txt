set(srcs
    "annotation.cc"
    "annotation.h"
    "annotation_list.cc"
    "annotation_list.h"
    "crash_report_database.cc"
    "crash_report_database.h"
    "crashpad_client.h"
    "crashpad_info.cc"
    "crashpad_info.h"
    "prune_crash_reports.cc"
    "prune_crash_reports.h"
    "settings.cc"
    "settings.h"
    "simple_address_range_bag.h"
    "simple_string_dictionary.h"
    "simulate_crash.h"
)
add_library(client STATIC ${srcs})
target_link_libraries(client PUBLIC base util)

if(APPLE)
    target_sources(client PRIVATE
        "crash_report_database_mac.mm"
        "crashpad_client_mac.cc"
        "simulate_crash_mac.cc"
        "simulate_crash_mac.h"
    )
endif()

if(UNIX)
    target_sources(client PRIVATE
        "crashpad_client_linux.cc"
        "simulate_crash_linux.h"
        "client_argv_handling.cc"
        "client_argv_handling.h"
        "crashpad_info_note.S"
        "crash_report_database_generic.cc"
    )
    target_link_libraries(client PUBLIC lss)
endif()

if(WIN32)
    target_sources(client PRIVATE
        "crash_report_database_win.cc"
        "crashpad_client_win.cc"
        "simulate_crash_win.h"
    )
    target_link_libraries(client PUBLIC "rpcrt4.lib")
    target_compile_options(client PRIVATE "/wd4201")    # nonstandard extension used : nameless struct/union
endif()

install(TARGETS client)
install(DIRECTORY "./" DESTINATION "include/client"
    FILES_MATCHING PATTERN "*.h")
