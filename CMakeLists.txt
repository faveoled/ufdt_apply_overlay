cmake_minimum_required(VERSION 3.10)
project(ufdt_apply_overlay)

# System dependencies library
add_library(ufdt-sysdeps STATIC
    sysdeps/libufdt_sysdeps_posix.c
)
target_include_directories(ufdt-sysdeps PUBLIC
    sysdeps/include
)

# ufdt library
add_library(ufdt STATIC
    ufdt_convert.c
    ufdt_node.c
    ufdt_node_pool.c
    ufdt_overlay.c
    ufdt_prop_dict.c
)
target_include_directories(ufdt PUBLIC
    include
)
target_link_libraries(ufdt PUBLIC ufdt-sysdeps)


# Main executable as per user request
add_executable(ufdt_apply_overlay
    tests/src/fdt_overlay_test_app.c
    tests/src/util.c
)
target_include_directories(ufdt_apply_overlay PRIVATE
    tests/src
)
target_link_libraries(ufdt_apply_overlay
    PRIVATE
    ufdt
    fdt
)

# Create a symlink for the test script, which expects fdt_apply_overlay to exist
add_custom_command(
    TARGET ufdt_apply_overlay POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E create_symlink
            $<TARGET_FILE_NAME:ufdt_apply_overlay>
            fdt_apply_overlay
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
)
