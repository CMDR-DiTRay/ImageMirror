cmake_minimum_required(VERSION 3.5)

project(imagemirror)

add_executable(imagemirror main.cpp)

find_package(ImageMagick 6.9 COMPONENTS Magick++)
if(ImageMagick_FOUND)
    # Find Imagemagick Library directory
    get_filename_component(MAGICK_LIB_DIR ${ImageMagick_MagickCore_LIBRARY} DIRECTORY)
    
    # Find where Magick++-config lives
    file(GLOB_RECURSE MAGICK_CONFIG FOLLOW_SYMLINKS ${MAGICK_LIB_DIR}/Magick++-config)
    
    # Ask about CXX and lib flags/locations
    set(MAGICK_CONFIG ${MAGICK_CONFIG} CACHE STRING "Path to Magick++-config utility")
    execute_process(COMMAND "${MAGICK_CONFIG}" "--cxxflags" OUTPUT_VARIABLE MAGICK_CXX_FLAGS)
    execute_process(COMMAND "${MAGICK_CONFIG}" "--libs" OUTPUT_VARIABLE MAGICK_LD_FLAGS)
    # Add these to cache
    set(MAGICK_CXX_FLAGS "${MAGICK_CXX_FLAGS}" CACHE STRING "ImageMagick configuration specific compilation flags.")
    set(MAGICK_LD_FLAGS  "${MAGICK_LD_FLAGS}" CACHE STRING "ImageMagick configuration specific linking flags.")
    
    # Split into list:
    string(REGEX MATCHALL "([^\ ]+)" MAGICK_CXX_FLAGS "${MAGICK_CXX_FLAGS}")
    string(REGEX MATCHALL "([^\ ]+)" MAGICK_LD_FLAGS "${MAGICK_LD_FLAGS}")
    
    # Remove trailing whitespace (CMAKE warns about this)
    string(STRIP "${MAGICK_CXX_FLAGS}" MAGICK_CXX_FLAGS)
    string(STRIP "${MAGICK_LD_FLAGS}" MAGICK_LD_FLAGS)
    
    # Apply CXX flags and LD flags to the target imagemirror
    target_compile_options(imagemirror PUBLIC ${MAGICK_CXX_FLAGS})
    target_link_libraries(imagemirror PUBLIC ${MAGICK_LD_FLAGS})
endif(ImageMagick_FOUND)

install(TARGETS imagemirror RUNTIME DESTINATION bin)
