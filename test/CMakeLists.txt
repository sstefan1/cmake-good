if(CMAKE_CXX_XOMPILER_ID MATCHES "MSVC")
    message("[MESSAGE]: MSVC FOUND")

	# Download and unpack googletest at configure time
	configure_file(CMakeLists.txt.in googletest-download/CMakeLists.txt)
	execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
	  RESULT_VARIABLE result
	  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download )
	if(result)
	  message(FATAL_ERROR "CMake step for googletest failed: ${result}")
	endif()
	execute_process(COMMAND ${CMAKE_COMMAND} --build .
	  RESULT_VARIABLE result
	  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download )
	if(result)
	  message(FATAL_ERROR "Build step for googletest failed: ${result}")
	endif()

	# Prevent overriding the parent project's compiler/linker
	# settings on Windows
	set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

	# Add googletest directly to our build. This defines
	# the gtest and gtest_main targets.
	add_subdirectory(${CMAKE_BINARY_DIR}/googletest-src
	                 ${CMAKE_BINARY_DIR}/googletest-build
	                 EXCLUDE_FROM_ALL)

	# The gtest/gtest_main targets carry header search path
	# dependencies automatically when using CMake 2.8.11 or
	# later. Otherwise we have to add them here ourselves.
	if (CMAKE_VERSION VERSION_LESS 2.8.11)
	  include_directories("${gtest_SOURCE_DIR}/include")
	endif()

	# Now simply link against gtest or gtest_main as needed. Eg
	add_executable(example example.cpp)
	target_link_libraries(example gtest_main)
	add_test(NAME example_test COMMAND example)
else()
    include(ExternalProject)

    # set default ExternalProject root directory.
    SET_DIRECTORY_PROPERTIES(PROPERTIES EP_PREFIX ${CMAKE_BINARY_DIR}/third_party)

    # add gtest.
    ExternalProject_Add(
        googletest
        GIT_REPOSITORY https://github.com/google/googletest/
        #SVN_REPOSITORY http://googletest.googlecode.com/svn/trunk/
        # BUILD_BYPRODUCTS ${binary_dir}/googlemock/gtest/libgtest.a
        PREFIX ${CMAKE_CURRENT_BINARY_DIR}/gtest
        INSTALL_COMMAND ""
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
    )

    ExternalProject_Get_Property(googletest source_dir binary_dir)
    # specify the include dir
    set(GTEST_INCLUDE_DIR ${source_dir}/include)

    # library
    set(GTEST_LIBRARY_PATH ${binary_dir}/$CMAKE_FIND_LIBRARY_PREFIXES}gtest.a)
    set(GTEST_LIBRARY gtest)
    add_library(${GTEST_LIBRARY} UNKNOWN IMPORTED)
    set_property(TARGET ${GTEST_LIBRARY} PROPERTY IMPORTED_LOCATION
        ${GTEST_LIBRARY_PATH} )
    add_dependencies(${GTEST_LIBRARY} googletest)

    add_executable(gtest_ex example.cpp)

    #target_include_directories(gtest_ex PUBLIC ${source_dir}/include)
    #target_link_libraries(gtest_ex ${binary_dir}/googlemock/gtest/libgtest.a)

    enable_testing()
    add_test(NAME gtest_ex
             COMMAND gtest_ex)
endif()
