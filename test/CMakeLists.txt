# Download and unpack googletest at configure time
configure_file(CMakeLists.txt.in googletest-download/CMakeLists.txt)
execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download )
if(result)
  message(FATAL_ERROR "CMake step for googletest failed: ${result}")
endif()
execute_process(COMMAND ${CMAKE_COMMAND} --build .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download )
if(result)
  message(FATAL_ERROR "Build step for googletest failed: ${result}")
endif()

# Prevent overriding the parent project's compiler/linker
# settings on Windows
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# Add googletest directly to our build. This defines
# the gtest and gtest_main targets.
add_subdirectory(${CMAKE_CURRENT_BINARY_DIR}/googletest-src
    ${CMAKE_CURRENT_BINARY_DIR}/googletest-build
                 EXCLUDE_FROM_ALL)

# The gtest/gtest_main targets carry header search path
# dependencies automatically when using CMake 2.8.11 or
# later. Otherwise we have to add them here ourselves.
if (CMAKE_VERSION VERSION_LESS 2.8.11)
  include_directories("${gtest_SOURCE_DIR}/include")
endif()

# Now simply link against gtest or gtest_main as needed. Eg
add_executable(gtest_ex example.cpp)
target_link_libraries(gtest_ex gtest_main)
add_test(NAME example_test COMMAND example)

############################################
#     # download and build googletest
    # include(ExternalProject)

    # find_package(Threads REQUIRED)

    # # add gtest.
    # ExternalProject_Add(
        # googletest
        # GIT_REPOSITORY https://github.com/google/googletest/
        # #SVN_REPOSITORY http://googletest.googlecode.com/svn/trunk/
        # # BUILD_BYPRODUCTS ${binary_dir}/googlemock/gtest/libgtest.a
        # PREFIX ${CMAKE_CURRENT_BINARY_DIR}/gtest
        # INSTALL_COMMAND ""
    # )

    # ExternalProject_Get_Property(googletest source_dir binary_dir)

    # add_compile_options(-v)

    # # define a test
    # add_executable(gtest_ex example.cpp)

    # # include and link with gootletest.
    # add_dependencies(gtest_ex googletest)
    # include_directories(${source_dir}/googletest/include)
    # target_link_libraries(gtest_ex ${binary_dir}/googlemock/gtest/libgtest_main.a)
    # target_link_libraries(gtest_ex ${binary_dir}/googlemock/gtest/libgtest.a)
    # target_link_libraries(gtest_ex Threads::Threads)
    # #target_include_directories(gtest_ex PUBLIC ${source_dir}/include)
    # #target_link_libraries(gtest_ex ${binary_dir}/googlemock/gtest/libgtest.a)

    # enable_testing()
    # add_test(NAME gtest_ex
             # COMMAND gtest_ex)
# endif()
