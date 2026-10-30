cmake_minimum_required(VERSION 3.20)
project(myapp C)
set(CMAKE_C_STANDARD 11)

# GLFW: download and build from source
include(FetchContent)
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
FetchContent_Declare(glfw
  GIT_REPOSITORY https://github.com/glfw/glfw.git
  GIT_TAG 3.4)
FetchContent_MakeAvailable(glfw)

find_package(OpenGL REQUIRED)

# glad: a normal small library
add_library(glad STATIC third_party/glad/src/glad.c)
target_include_directories(glad PUBLIC third_party/glad/include)

# Your program
add_executable(myapp
  src/main.c
  src/audio_impl.c
  src/nuklear_impl.c)

# Folders where #include "nuklear.h" and "miniaudio.h" are found
target_include_directories(myapp PRIVATE
  src
  third_party/nuklear
  third_party/miniaudio)

target_link_libraries(myapp PRIVATE glfw OpenGL::GL glad)

# Extra system libraries these header-only libs need on Linux/macOS
if(UNIX)
  target_link_libraries(myapp PRIVATE m ${CMAKE_DL_LIBS})
  find_package(Threads REQUIRED)
  target_link_libraries(myapp PRIVATE Threads::Threads)
endif()