cmake_minimum_required(VERSION 3.0)

project(imagemirror)

add_executable(imagemirror main.cpp)

install(TARGETS imagemirror RUNTIME DESTINATION bin)
