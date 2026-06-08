cmake_minimum_required(VERSION 3.16)

project(movingai-demos VERSION 0.9 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ── Locate hog2 ───────────────────────────────────────────────────────────────
# Search ~/.local first (default hog2 install prefix), then standard paths.
list(PREPEND CMAKE_PREFIX_PATH "$ENV{HOME}/.local")
find_package(hog2 REQUIRED)

# ── One executable per subdirectory ──────────────────────────────────────────
file(GLOB _demo_dirs LIST_DIRECTORIES true RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" "*")

foreach(_dir ${_demo_dirs})
    if(NOT IS_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/${_dir}")
        continue()
    endif()

    # Collect all .cpp files in the subdirectory, excluding editor temp files (e.g. .#foo.cpp)
    file(GLOB _srcs "${CMAKE_CURRENT_SOURCE_DIR}/${_dir}/*.cpp")
    list(FILTER _srcs EXCLUDE REGEX "/\\.#")
    if(NOT _srcs)
        continue()
    endif()

    add_executable(${_dir} ${_srcs})
    target_link_libraries(${_dir} PRIVATE hog2::hog2)

    # Put all binaries in a common output directory
    set_target_properties(${_dir} PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
    )
endforeach()
