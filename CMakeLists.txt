# CMakeLists.txt
cmake_minimum_required(VERSION 3.15)
project(StaticAudioLibs LANGUAGES C CXX)

# Set output directories consistently across all configurations on Windows
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Specify output directories for each configuration in multi-config generators (like Visual Studio)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/bin)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR}/bin)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_BINARY_DIR}/bin)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_MINSIZEREL ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_MINSIZEREL ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_MINSIZEREL ${CMAKE_BINARY_DIR}/bin)



# Define options to control static linking
set(BUILD_SHARED_LIBS OFF CACHE BOOL "Build static libraries")

# Set the output directory for static libraries
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# Options to disable docs, tests, and SIMD for portability
set(OPUS_BUILD_TESTING OFF CACHE BOOL "Disable Opus tests" FORCE)
set(OPUS_BUILD_PROGRAMS OFF CACHE BOOL "Do not build Opus programs" FORCE)
# set(OPUS_X86_MAY_HAVE_SSE OFF CACHE BOOL "Disable Opus SSE optimizations" FORCE)
# set(OPUS_X86_MAY_HAVE_SSE2 OFF CACHE BOOL "Disable Opus SSE2 optimizations" FORCE)
# set(OPUS_X86_MAY_HAVE_SSE4_1 OFF CACHE BOOL "Disable Opus SSE4.1 optimizations" FORCE)
# set(OPUS_X86_MAY_HAVE_AVX2 OFF CACHE BOOL "Disable Opus AVX2 optimizations" FORCE)
set(OPUS_X86_PRESUME_SSE OFF CACHE BOOL "Disable Opus presumed SSE optimizations" FORCE)
set(OPUS_X86_PRESUME_SSE2 OFF CACHE BOOL "Disable Opus presumed SSE2 optimizations" FORCE)
set(OPUS_X86_PRESUME_SSE4_1 OFF CACHE BOOL "Disable Opus presumed SSE4.1 optimizations" FORCE)
set(OPUS_X86_PRESUME_AVX2 OFF CACHE BOOL "Disable Opus presumed AVX2 optimizations" FORCE)

# Disable optional components in FLAC
set(BUILD_CXXLIBS OFF CACHE BOOL "Disable FLAC++ library" FORCE)               # FLAC++ library
set(BUILD_PROGRAMS OFF CACHE BOOL "Do not build FLAC programs")
set(BUILD_EXAMPLES OFF CACHE BOOL "Disable FLAC examples" FORCE)                # Examples
set(BUILD_DOCS OFF CACHE BOOL "Disable FLAC documentation" FORCE)               # Documentation
set(BUILD_TESTING OFF CACHE BOOL "Disable FLAC tests" FORCE)                    # Tests
set(INSTALL_MANPAGES OFF CACHE BOOL "Disable FLAC man pages" FORCE)             # Man pages
set(FLAC__NO_ASM TRUE CACHE BOOL "Disable FLAC SIMD" FORCE)                     # SIMD optimizations
set(FLAC__HAS_OGG FALSE CACHE BOOL "Disable Ogg support in FLAC" FORCE)         # Ogg support (if unnecessary)
set(FLAC__NO_LIBUTF8 TRUE CACHE BOOL "Disable libutf8 for FLAC" FORCE) 

# Add dependencies in the correct order and set necessary flags
add_subdirectory(ogg)
set(OGG_LIBRARY ogg)
set(OGG_INCLUDE_DIR ${CMAKE_SOURCE_DIR}/ogg/include)

add_subdirectory(vorbis)
target_link_libraries(vorbis PRIVATE ogg)
target_include_directories(vorbis PRIVATE ${CMAKE_SOURCE_DIR}/ogg/include)

add_subdirectory(opus)
target_include_directories(opus PRIVATE ${CMAKE_SOURCE_DIR}/opus/include)

add_subdirectory(flac)

# Conditionally exclude unnecessary targets if they exist
if(TARGET replaygain_analysis)
    set_property(TARGET replaygain_analysis PROPERTY EXCLUDE_FROM_ALL TRUE)
endif()

if(TARGET replaygain_synthesis)
    set_property(TARGET replaygain_synthesis PROPERTY EXCLUDE_FROM_ALL TRUE)
endif()

if(TARGET getopt)
    set_property(TARGET getopt PROPERTY EXCLUDE_FROM_ALL TRUE)
endif()

if(TARGET grabbag)
    set_property(TARGET grabbag PROPERTY EXCLUDE_FROM_ALL TRUE)
endif()

if(TARGET utf8)
    set_property(TARGET utf8 PROPERTY EXCLUDE_FROM_ALL TRUE)
endif()

if(TARGET benchmark_residual)
    set_property(TARGET benchmark_residual PROPERTY EXCLUDE_FROM_ALL TRUE)
endif()

# Add Opusfile
add_subdirectory(opusfile)
target_link_libraries(opusfile PRIVATE opus ogg)
target_include_directories(opusfile PRIVATE ${CMAKE_SOURCE_DIR}/opus/include ${CMAKE_SOURCE_DIR}/ogg/include)

# Add libopusenc
add_subdirectory(libopusenc)
target_link_libraries(opusenc PRIVATE opus ogg)
target_include_directories(opusenc PRIVATE ${CMAKE_SOURCE_DIR}/opus/include ${CMAKE_SOURCE_DIR}/ogg/include)
