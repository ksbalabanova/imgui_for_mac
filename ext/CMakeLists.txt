if(APPLE)
    enable_language(OBJC)
endif()

set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
add_subdirectory("${CMAKE_CURRENT_SOURCE_DIR}/glfw")

find_package(OpenGL REQUIRED)

add_subdirectory("${CMAKE_CURRENT_SOURCE_DIR}/sdl2")

set(ROOT "${CMAKE_CURRENT_SOURCE_DIR}/imgui")

add_library(imgui STATIC
        ${ROOT}/imgui.cpp
        ${ROOT}/imgui.h
        ${ROOT}/imstb_rectpack.h
        ${ROOT}/imstb_textedit.h
        ${ROOT}/imstb_truetype.h
        ${ROOT}/imgui_demo.cpp
        ${ROOT}/imgui_draw.cpp
        ${ROOT}/imgui_internal.h
        ${ROOT}/imgui_tables.cpp
        ${ROOT}/imgui_widgets.cpp
)
target_include_directories(imgui PUBLIC
        $<BUILD_INTERFACE:${ROOT}>
        $<INSTALL_INTERFACE:include>)

set(INSTALL_TARGETS imgui)
set(INSTALL_HEADERS ${ROOT}/imgui.h ${ROOT}/imconfig.h)

foreach(BACKEND glfw opengl3 sdl2 sdlrenderer2)
    set(NAME imgui_impl_${BACKEND})
    set(HEADER ${ROOT}/backends/${NAME}.h)
    add_library(${NAME} STATIC ${ROOT}/backends/${NAME}.cpp ${HEADER})
    target_link_libraries(${NAME} PUBLIC imgui)
    target_include_directories(${NAME} PUBLIC
            $<BUILD_INTERFACE:${ROOT}/backends>
            $<INSTALL_INTERFACE:include>)
    LIST(APPEND INSTALL_TARGETS ${NAME})
    LIST(APPEND INSTALL_HEADERS ${HEADER})
endforeach()

target_link_libraries(imgui_impl_glfw PRIVATE glfw)
target_sources(imgui_impl_opengl3 PRIVATE ${ROOT}/backends/imgui_impl_opengl3_loader.h)
target_link_libraries(imgui_impl_opengl3 PRIVATE OpenGL::GL)
target_link_libraries(imgui_impl_sdl2 PRIVATE SDL3::SDL3)
target_link_libraries(imgui_impl_sdlrenderer2 PRIVATE SDL3::SDL3)
