set(TINYEXR_ROOT "${CMAKE_SOURCE_DIR}/tinyexr")

set(TINYEXR_UNITY_SOURCES
    ${TINYEXR_ROOT}/deps/miniz/miniz.c
    ${TINYEXR_ROOT}/deps/miniz/miniz.h
    ${TINYEXR_ROOT}/exr_reader.hh
    ${TINYEXR_ROOT}/streamreader.hh
    ${TINYEXR_ROOT}/tinyexr.cc
    ${TINYEXR_ROOT}/tinyexr.h
    tinyexr_wrapper.cpp
)

if(WIN32)
    list(APPEND TINYEXR_UNITY_SOURCES tinyexr.def)
endif()

add_library(TinyExr SHARED ${TINYEXR_UNITY_SOURCES})

target_include_directories(TinyExr PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${TINYEXR_ROOT}
    ${TINYEXR_ROOT}/deps/miniz
)
target_compile_definitions(TinyExr PRIVATE TINYEXR_USE_MINIZ=1)

if(MSVC)
    target_compile_definitions(TinyExr PRIVATE NOMINMAX _CRT_SECURE_NO_WARNINGS)
endif()

set(TINYEXR_UNITY_PLUGIN_DIR "${CMAKE_SOURCE_DIR}/../TinyExr/x64")

set_target_properties(TinyExr PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY "${TINYEXR_UNITY_PLUGIN_DIR}"
    LIBRARY_OUTPUT_DIRECTORY "${TINYEXR_UNITY_PLUGIN_DIR}"
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
)

foreach(CONFIG_TYPE DEBUG RELEASE RELWITHDEBINFO MINSIZEREL)
    string(TOUPPER "${CONFIG_TYPE}" CONFIG_TYPE_UPPER)
    set_target_properties(TinyExr PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY_${CONFIG_TYPE_UPPER} "${TINYEXR_UNITY_PLUGIN_DIR}"
        LIBRARY_OUTPUT_DIRECTORY_${CONFIG_TYPE_UPPER} "${TINYEXR_UNITY_PLUGIN_DIR}"
        ARCHIVE_OUTPUT_DIRECTORY_${CONFIG_TYPE_UPPER} "${CMAKE_BINARY_DIR}/lib/${CONFIG_TYPE}"
    )
endforeach()
