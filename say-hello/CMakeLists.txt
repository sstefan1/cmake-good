add_library(say-hello
    src/say-hello/hello.cpp
    include/say-hello/hello.h
    )

target_include_directories(say-hello PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include")
