cmake_minimum_required(VERSION 3.19)
project(doom++)

set(CMAKE_CXX_STANDARD 20)

#include(cmake/CompilerWarnings.cmake)

add_executable(${PROJECT_NAME} main.cpp
        core/game_data.cpp
        game/arena.cpp
        game/arena.hpp
        game/demo_screen.cpp
        game/demo_screen.hpp
        game/mobj.hpp
        game/system.cpp
        grfx/icon.cpp
        grfx/sdl_system.cpp
        grfx/system.cpp
        menu/draw_text.cpp
        menu/system.cpp
        rndr/bsp_renderer.cpp
        rndr/column.cpp
        rndr/system.cpp
        rndr/trigonometry.cpp
        rndr/visplane.cpp
        rndr/visplane.hpp
        core/real.hpp
        game/level.cpp)


## Setup Conan < 2.0
if (NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
        message(STATUS "Downloading conan.cmake from https://github.com/conan-io/cmake-conan")
        file(DOWNLOAD
          "https://raw.githubusercontent.com/conan-io/cmake-conan/master/conan.cmake"
          "${CMAKE_BINARY_DIR}/conan.cmake"
        )
endif ()


include(${CMAKE_BINARY_DIR}/conan.cmake)
conan_cmake_autodetect(settings)
conan_check(REQUIRED)
conan_cmake_run(CONANFILE conanfile.txt
  BASIC_SETUP NO_OUTPUT_DIRS
  BUILD missing
  SETTINGS ${settings}
)


## Find third-party packages
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_BINARY_DIR})
find_package(fmt REQUIRED)
find_package(SDL2 2.0.1 REQUIRED)

message(STATUS "
CMAKE_CURRENT_LIST_DIR= ${CMAKE_CURRENT_LIST_DIR}
CMAKE_CURRENT_BINARY_DIR= ${CMAKE_CURRENT_BINARY_DIR}
")
#set_project_warnings(${PROJECT_NAME})
target_compile_definitions(${PROJECT_NAME} PRIVATE
        ROOT_DIR="${CMAKE_CURRENT_LIST_DIR}"
        PACKAGE_NAME="${PROJECT_NAME}"
        )

target_compile_options(${PROJECT_NAME} PRIVATE -fconcepts-diagnostics-depth=10 -fsanitize=address -fno-omit-frame-pointer)
target_link_options(${PROJECT_NAME} PRIVATE -fsanitize=address)
target_include_directories(${PROJECT_NAME} PRIVATE ./)
target_link_libraries(${PROJECT_NAME} fmt::fmt SDL2::SDL2)
