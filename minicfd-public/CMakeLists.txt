# General setup
# =============
cmake_minimum_required(VERSION 3.16)

project(
    MiniCFD
    VERSION 0.0.1
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Target setup
# ============
add_library(simulation
    src/Simulation.cpp
)

add_executable(minicfd
    src/MiniCFD.cpp
)

target_link_libraries(minicfd PRIVATE simulation)


# Dependency setup
# ================
find_package(OpenMP REQUIRED)
if(OpenMP_CXX_FOUND)
    target_link_libraries(simulation PUBLIC OpenMP::OpenMP_CXX)
endif()

include(FetchContent)
FetchContent_Declare(
    cxxopts
    GIT_REPOSITORY https://github.com/jarro2783/cxxopts
    GIT_TAG        v3.2.0
)
FetchContent_MakeAvailable(cxxopts)
target_link_libraries(minicfd PRIVATE cxxopts)

include(FetchContent)
FetchContent_Declare(
    spdlog
    GIT_REPOSITORY https://github.com/gabime/spdlog.git
    GIT_TAG        v1.13.0
)
FetchContent_MakeAvailable(spdlog)
target_link_libraries(minicfd PRIVATE spdlog::spdlog_header_only)
target_link_libraries(simulation PUBLIC spdlog::spdlog_header_only)


# Options and tests
# =================
option(ENABLE_TRACE_LOG "Print log with level TRACE." OFF)
option(BUILD_TESTS "Build test suite" OFF)

if(ENABLE_TRACE_LOG)
    target_compile_definitions(minicfd PRIVATE SPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_TRACE)
    target_compile_definitions(simulation PRIVATE SPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_TRACE)
else()
    target_compile_definitions(minicfd PRIVATE SPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_DEBUG)
    target_compile_definitions(simulation PRIVATE SPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_DEBUG)
endif()


if(BUILD_TESTS)
    add_subdirectory(tests)
endif()
