cmake_minimum_required(VERSION 3.16)
project(MapleStory VERSION 1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug)
endif()

find_package(OpenGL REQUIRED)
find_package(glfw3 REQUIRED)
find_package(GLEW REQUIRED)
find_package(Freetype REQUIRED)

# SDL2 and SDL2_mixer (manual paths to avoid cmake-config dependency chain)
find_path(SDL2_INCLUDE_DIR SDL.h HINTS /opt/homebrew/include/SDL2)
find_library(SDL2_LIBRARY SDL2 HINTS /opt/homebrew/lib)
find_path(SDL2_MIXER_INCLUDE_DIR SDL_mixer.h HINTS /opt/homebrew/Cellar/sdl2_mixer/2.8.1_1/include/SDL2 /opt/homebrew/include/SDL2)
find_library(SDL2_MIXER_LIBRARY SDL2_mixer HINTS /opt/homebrew/lib /opt/homebrew/Cellar/sdl2_mixer/2.8.1_1/lib)

if(NOT SDL2_INCLUDE_DIR OR NOT SDL2_LIBRARY)
    message(FATAL_ERROR "SDL2 not found")
endif()
if(NOT SDL2_MIXER_INCLUDE_DIR OR NOT SDL2_MIXER_LIBRARY)
    message(FATAL_ERROR "SDL2_mixer not found")
endif()
find_library(LZ4_LIBRARY lz4 REQUIRED)
find_path(LZ4_INCLUDE_DIR lz4.h REQUIRED)
add_library(Lz4::Lz4 INTERFACE IMPORTED)
set_target_properties(Lz4::Lz4 PROPERTIES
    INTERFACE_LINK_LIBRARIES "${LZ4_LIBRARY}"
    INTERFACE_INCLUDE_DIRECTORIES "${LZ4_INCLUDE_DIR}"
)

find_path(ASIO_INCLUDE_DIR asio.hpp REQUIRED)
if(NOT ASIO_INCLUDE_DIR)
    message(FATAL_ERROR "asio.hpp not found. brew install asio")
endif()

# NoLifeNx
set(NLNX_SOURCES
    includes/nlnx/audio.cpp
    includes/nlnx/bitmap.cpp
    includes/nlnx/file.cpp
    includes/nlnx/node.cpp
    includes/nlnx/nx.cpp
)
add_library(NoLifeNx STATIC ${NLNX_SOURCES})
target_include_directories(NoLifeNx PUBLIC includes ${LZ4_INCLUDE_DIR})
target_link_libraries(NoLifeNx PUBLIC Lz4::Lz4)

# Main client sources
file(GLOB SOURCES
    Audio/*.cpp
    Character/*.cpp
    Character/Inventory/*.cpp
    Character/Look/*.cpp
    Configuration.cpp
    Data/*.cpp
    Gameplay/*.cpp
    Gameplay/Combat/*.cpp
    Gameplay/Maplemap/*.cpp
    Gameplay/Physics/*.cpp
    Graphics/*.cpp
    IO/*.cpp
    IO/Components/*.cpp
    IO/UITypes/*.cpp
    MapleStory.cpp
    Net/*.cpp
    Net/Handlers/*.cpp
    Net/Handlers/Helpers/*.cpp
    Util/*.cpp
)

add_executable(MapleStory ${SOURCES})

target_include_directories(MapleStory PRIVATE
    ${CMAKE_SOURCE_DIR}
    includes
    includes/stb
    ${ASIO_INCLUDE_DIR}
    ${SDL2_INCLUDE_DIR}
    ${SDL2_MIXER_INCLUDE_DIR}
)

target_compile_definitions(MapleStory PRIVATE
    USE_ASIO USE_NX USE_CRYPTO GLEW_STATIC
)

if(APPLE)
    target_compile_options(MapleStory PRIVATE -Wno-c++11-narrowing)
endif()

target_link_libraries(MapleStory PRIVATE
    NoLifeNx
    OpenGL::GL
    glfw
    GLEW::GLEW
    Freetype::Freetype
    ${SDL2_LIBRARY}
    ${SDL2_MIXER_LIBRARY}
    Lz4::Lz4
    pthread
)

if(APPLE)
    target_link_libraries(MapleStory PRIVATE
        "-framework CoreFoundation" "-framework CoreGraphics" "-framework IOKit"
    )
endif()
