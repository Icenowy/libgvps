cmake_minimum_required(VERSION 3.0)
project(libgvps)

if(NOT MSVC)
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Og")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
else()
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Ox /GL")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
endif()

aux_source_directory(. GVPS_SRC_SOURCES)
add_library(gvps STATIC ${GVPS_SRC_SOURCES})

set_target_properties(gvps PROPERTIES
    CXX_STANDARD 11
    C_STANDARD 99
    POSITION_INDEPENDENT_CODE ON
)


# Arrange header files for external use
add_custom_command(TARGET gvps POST_BUILD COMMAND ${CMAKE_COMMAND} -E make_directory "include")
add_custom_command(TARGET gvps POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy "${CMAKE_CURRENT_SOURCE_DIR}/gvps.h" "include/gvps.h")
file(GLOB_RECURSE GVPS_SRC_HEADERS RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}/src" "src/*.h")
target_include_directories(gvps INTERFACE "${CMAKE_CURRENT_BINARY_DIR}/include")


if("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}")  # Test subproject
    install(TARGETS gvps
        RUNTIME DESTINATION "bin"
        LIBRARY DESTINATION "lib"
        ARCHIVE DESTINATION "lib"
    )
    install(DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/include/"
        DESTINATION "include"
    )
endif()
