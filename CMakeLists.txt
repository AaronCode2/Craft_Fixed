cmake_minimum_required(VERSION 3.15..3.10)

project(craft C)

file(GLOB SOURCE_FILES src/*.c)

add_executable(craft
    ${SOURCE_FILES}
    deps/glew/src/glew.c
    deps/lodepng/lodepng.c
    deps/noise/noise.c
    deps/sqlite/sqlite3.c
    deps/tinycthread/tinycthread.c
)

target_compile_features(craft PRIVATE c_std_99)
target_compile_options(craft PRIVATE -O3)

add_subdirectory(deps/glfw)

add_custom_command(
    TARGET craft
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
            ${CMAKE_SOURCE_DIR}/textures
            $<TARGET_FILE_DIR:craft>/textures
)

add_custom_command(
    TARGET craft
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
            ${CMAKE_SOURCE_DIR}/shaders
            $<TARGET_FILE_DIR:craft>/shaders
)

target_include_directories(craft PRIVATE
    deps/glew/include
    deps/glfw/include
    deps/lodepng
    deps/noise
    deps/sqlite
    deps/tinycthread
)

find_package(CURL REQUIRED)

if(MINGW)
    target_link_libraries(craft
        glfw
        opengl32
        gdi32
        user32
        ws2_32
        ${CURL_LIBRARIES}
    )
elseif(APPLE)
    target_link_libraries(craft
        glfw
        CURL::libcurl
    )
elseif(UNIX)
    target_link_libraries(craft
        dl
        glfw
        CURL::libcurl
    )
endif()
