### Macros

macro(require_cpp17)
        if (${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION} GREATER 3.8)
                # CMake > 3.1 has supported version checks, but only > 3.8 supports C++17
                set(CMAKE_CXX_STANDARD 17)
                set(CMAKE_CXX_STANDARD_REQUIRED ON)
        else()
            if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
                message ("-- GNU CXX (-std=c++0x)")
                list(APPEND CMAKE_CXX_FLAGS "-std=c++0x")
            elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
                message ("-- CLang CXX (-std=c++0x)")
                list(APPEND CMAKE_CXX_FLAGS "-std=c++0x")
            elseif ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Intel")
                # using Intel C++
            elseif ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
                # using Visual Studio C++
            else()
                message("Unknown compiler - libhuffman requires C++17")
                list(APPEND CMAKE_CXX_FLAGS "-std=c++0x")
            endif()
        endif()
endmacro()

### Main CMake configuration

# Setup project
cmake_minimum_required(VERSION 3.1)

project(huffman CXX)

set(${PROJECT_NAME}_VERSION_MAJOR 0)
set(${PROJECT_NAME}_VERSION_MINOR 0)
set(${PROJECT_NAME}_VERSION_PATCH 1)
set(${PROJECT_NAME}_VERSION_STRING "${PROJECT_NAME}_VERSION_MAJOR.${PROJECT_NAME}_VERSION_MINOR.${PROJECT_NAME}_VERSION_PATCH")

### Settings
option(BUILD_STATIC_LIB "Build libhuffman as a static library" OFF)
option(BUILD_SHARED_LIB "Build libhuffman as a shared library" ON)
option(UNIT_TESTING "Build all tests" OFF)
option(LIB_INSTALL_DIR "Default installation directory for the resulting library" "/usr/lib")
option(HEADER_INSTALL_DIR "Default installation directory for the resulting header" "/usr/include")

require_cpp17()

# Globally importing files is not "recommended", but manually specifying each file is retarded
# If there is a better way to say this, please let me know
file(GLOB SOURCES "src/*.cpp")

# Set the include directories
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

if(BUILD_SHARED_LIB)
    add_library(${PROJECT_NAME} SHARED ${SOURCES})
endif()

if(BUILD_STATIC_LIB)
    add_library(${PROJECT_NAME} STATIC ${SOURCES})
endif()

# Install library
install(TARGETS ${PROJECT_NAME} DESTINATION "${LIB_INSTALL_DIR}/${PROJECT_NAME}")

# Install the API header
file(GLOB HEADERS "include/huffman.h")
install(FILES ${HEADERS} DESTINATION "${HEADER_INSTALL_DIR}/${PROJECT_NAME}")

export(PACKAGE ${PROJECT_NAME})

### Unit Testing

if (UNIT_TESTING)

    # We need C++ 17
    require_cpp17()

    # Download and install GoogleTest

    include(ExternalProject)
    ExternalProject_Add(gtest
      URL https://github.com/google/googletest/archive/release-1.8.0.zip
      # Comment above line, and uncomment line below to use subversion.
      # SVN_REPOSITORY http://googletest.googlecode.com/svn/trunk/ 
      # Uncomment line below to freeze a revision (here the one for 1.7.0)
      # SVN_REVISION -r700

      PREFIX ${CMAKE_CURRENT_BINARY_DIR}/gtest
      INSTALL_COMMAND ""
    )
    
    ExternalProject_Get_Property(gtest source_dir binary_dir)

    # Define a test
    add_executable(huffman_test "tests/huffman_test.cpp")

    # Configure the test to use GoogleTest
    # If used often, could be made a macro.
    add_dependencies(huffman_test gtest)
    include_directories(${source_dir}/include)
    target_link_libraries(huffman_test ${binary_dir}/libgtest.a)
    target_link_libraries(huffman_test ${binary_dir}/libgtest_main.a)

    # Just make the test runnable with
    # $ make test

    enable_testing()
    add_test(NAME    huffman_test 
             COMMAND huffman_test)

endif()