# Written by Daniel Schreij (dschreij@gmail.com) on 11 Nov 2016
# This procedure is configured to work in an Anaconda build environment (contiuum.io)
# In this environment, it should be called like 
# 
#    %LIBRARY_BIN%\cmake -G "NMake Makefiles" -DCMAKE_INSTALL_PREFIX:PATH="%LIBRARY_PREFIX%" -DCMAKE_BUILD_TYPE:STRING=Release ..
# 
# to be correctly configured
# 
# This file is intended to be used for Windows only! For other platforms, the supplied makefiles work very well.

set(PROJECT_NAME "SDL2_ttf")
project(${PROJECT_NAME})
CMAKE_MINIMUM_REQUIRED(VERSION 3.0.0)

set(IS_LIBRARY true)
set(CMAKE_INCLUDE_CURRENT_DIR TRUE)

#source files
set(SDLTTF_SOURCES
	SDL_ttf.c
)

include(GenerateExportHeader)

## Try to find SDL2 libraries
# SDL.h
FIND_PATH(SDL2_INCLUDE_DIR
    SDL.h
    PATH_SUFFIXES include/SDL2 include
)
# SDL2.lib
FIND_LIBRARY(SDL2_LIBRARY
    NAMES SDL2
    PATH_SUFFIXES lib64 lib lib/x64 lib/x86
)

if(SDL2_LIBRARY)
	message(STATUS "Found SDL2: ${SDL2_LIBRARY}")
else()
	message(FATAL_ERROR "SDL2 libraries could not be located." )
endif()

## Try to find freetype libraries
# SDL.h
FIND_PATH(FREETYPE_INCLUDE_DIR
    freetype.h
    PATH_SUFFIXES include/freetype include
)
# SDL2.lib
FIND_LIBRARY(FREETYPE_LIBRARY
    NAMES freetype libfreetype
    PATH_SUFFIXES lib
)

if(FREETYPE_LIBRARY-NOTFOUND)
	message(FATAL_ERROR "Freetype libraries could not be located." )
else()
	message(STATUS "Found freeype: ${FREETYPE_LIBRARY}")
endif()

# Set include dirs
# include_directories(./)
include_directories(${SDL2_INCLUDE_DIR})

# Create target library specs
add_library(${PROJECT_NAME} SHARED ${SDLTTF_SOURCES})
set_target_properties(${PROJECT_NAME} PROPERTIES DEFINE_SYMBOL "DLL_EXPORT")
target_link_libraries (${PROJECT_NAME} ${SDL2_LIBRARY} ${FREETYPE_LIBRARY})

##### Installation targets #####
install(TARGETS ${PROJECT_NAME}
  LIBRARY DESTINATION "lib${LIB_SUFFIX}"
  ARCHIVE DESTINATION "lib${LIB_SUFFIX}"
  RUNTIME DESTINATION bin)

# Copy .h files to include dir.
file(GLOB SDL2_TTF_INCLUDE_FILES ${CMAKE_CURRENT_SOURCE_DIR}/*.h)
install(FILES ${SDL2_TTF_INCLUDE_FILES} DESTINATION include/SDL2)
