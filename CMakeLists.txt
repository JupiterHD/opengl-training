cmake_minimum_required(VERSION 3.14)
project(opengl_training CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)

find_package(glfw3 3.4 QUIET)

if(NOT glfw3_FOUND)
    message(STATUS "GLFW не найден в системе — скачиваем и собираем...")
    FetchContent_Declare(
        glfw
        GIT_REPOSITORY https://github.com/glfw/glfw.git
        GIT_TAG        3.4
    	GIT_SHALLOW    TRUE
    )
    set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
    set(GLFW_BUILD_TESTS    OFF CACHE BOOL "" FORCE)
    set(GLFW_BUILD_DOCS     OFF CACHE BOOL "" FORCE)
    FetchContent_MakeAvailable(glfw)
else()
    message(STATUS "Используется системный GLFW (${glfw3_VERSION})")
endif()

set(GLEW_USE_STATIC_LIBS TRUE)
find_package(GLEW QUIET)

if(NOT GLEW_FOUND)
    message(STATUS "GLEW не найден в системе — скачиваем и собираем...")
    set(glew-cmake_BUILD_SHARED OFF CACHE BOOL "" FORCE)
    set(glew-cmake_BUILD_STATIC ON  CACHE BOOL "" FORCE)
    FetchContent_Declare(
        glew
        GIT_REPOSITORY https://github.com/Perlmint/glew-cmake.git
        GIT_TAG        glew-cmake-2.2.0
    )
    FetchContent_MakeAvailable(glew)
else()
    message(STATUS "Используется системный GLEW (${GLEW_VERSION})")
endif()

find_package(OpenGL REQUIRED)

add_executable(app app/main.cpp)

target_compile_definitions(app PRIVATE GLEW_STATIC)

if(GLEW_FOUND)
    set(GLEW_TARGET GLEW::GLEW)
else()
    set(GLEW_TARGET libglew_static)
endif()

target_link_libraries(app PRIVATE
    glfw
    ${GLEW_TARGET}
    OpenGL::GL
)