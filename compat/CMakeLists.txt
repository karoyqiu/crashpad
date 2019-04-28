add_library(compat STATIC)

if(APPLE)
    target_include_directories(compat PUBLIC "mac")
    target_sources(compat PRIVATE
        "mac/AvailabilityMacros.h"
        "mac/kern/exc_resource.h"
        "mac/mach-o/loader.h"
        "mac/mach/mach.h"
        "mac/sys/resource.h"
    )
else()
    target_include_directories(compat PUBLIC "non_mac")
    target_sources(compat PRIVATE
        "non_mac/mach-o/loader.h"
        "non_mac/mach/mach.h"
        "non_mac/mach/machine.h"
        "non_mac/mach/vm_prot.h"
    )
    target_link_libraries(compat PUBLIC xnu)
endif()

if(UNIX OR ANDROID)
    target_include_directories(compat PUBLIC "linux")
    target_sources(compat PRIVATE
        "linux/signal.h"
        "linux/sys/ptrace.h"
        "linux/sys/user.h"
    )
endif()

if(ANDROID)
    target_include_directories(compat PUBLIC "android")
    target_sources(compat PRIVATE
        "android/android/api-level.cc"
        "android/android/api-level.h"
        "android/dlfcn_internal.cc"
        "android/dlfcn_internal.h"
        "android/elf.h"
        "android/linux/elf.h"
        "android/linux/prctl.h"
        "android/linux/ptrace.h"
        "android/sched.h"
        "android/sys/epoll.cc"
        "android/sys/epoll.h"
        "android/sys/mman.cc"
        "android/sys/mman.h"
        "android/sys/syscall.h"
        "android/sys/user.h"
    )
endif()

if(WIN32)
    target_include_directories(compat PUBLIC "win")
    target_sources(compat PRIVATE
        "win/getopt.h"
        "win/strings.cc"
        "win/strings.h"
        "win/sys/types.h"
        "win/time.cc"
        "win/time.h"
        "win/winbase.h"
        "win/winnt.h"
        "win/winternl.h"
    )
    target_compile_definitions(compat PUBLIC "WIN32_LEAN_AND_MEAN" "NOMINMAX")
    target_link_libraries(compat PUBLIC getopt)
else()
    target_include_directories(compat PUBLIC "non_win")
    target_sources(compat PRIVATE
        "non_win/dbghelp.h"
        "non_win/minwinbase.h"
        "non_win/timezoneapi.h"
        "non_win/verrsrc.h"
        "non_win/windows.h"
        "non_win/winnt.h"
    )
endif()

if(NOT UNIX AND NOT ANDROID)
    target_include_directories(compat PUBLIC "non_elf")
    target_sources(compat PRIVATE "non_elf/elf.h")
    target_link_libraries(compat PUBLIC glibc)
endif()
