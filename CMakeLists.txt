cmake_minimum_required(VERSION 3.9.2)
project(cmake-good VERSION 1.0.0)

add_subdirectory(test)
add_subdirectory(say-hello)
add_subdirectory(bin)

#add_executable(hello main.cpp)

#target_link_libraries(hello PRIVATE say-hello)
