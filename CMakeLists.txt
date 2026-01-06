
cmake_minimum_required(VERSION 3.16)

project(imgui LANGUAGES C CXX)

option(IMGUI_BUILD_DEMO "Build imgui demo source" OFF)
option(IMGUI_BACKEND_SDL3 "Enable SDL3 backend" ON)
option(IMGUI_BACKEND_SDLGPU "Enable SDL GPU renderer backend" ON)

add_library(imgui STATIC)

add_library(imgui::imgui ALIAS imgui)

target_sources(imgui
    PRIVATE
        imgui.cpp
        imgui_draw.cpp
        imgui_tables.cpp
        imgui_widgets.cpp
)

if (IMGUI_BUILD_DEMO)
    target_sources(imgui PRIVATE imgui_demo.cpp)
endif()

target_include_directories(imgui
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/backends>
)

target_compile_features(imgui PUBLIC cxx_std_17)

if (IMGUI_BACKEND_SDL3)
    find_package(SDL3 CONFIG REQUIRED)

    target_sources(imgui
        PRIVATE
            backends/imgui_impl_sdl3.cpp
    )

    target_link_libraries(imgui PUBLIC SDL3::SDL3)

    target_compile_definitions(imgui PUBLIC IMGUI_IMPL_SDL3)
endif()

if (IMGUI_BACKEND_SDLGPU)
    target_sources(imgui
        PRIVATE
            backends/imgui_impl_sdlgpu3.cpp
    )

    target_compile_definitions(imgui PUBLIC IMGUI_IMPL_SDLGPU)
endif()

if (MSVC)
    target_compile_options(imgui PRIVATE /W4)
else()
    target_compile_options(imgui PRIVATE -Wall -Wextra -Wpedantic)
endif()
