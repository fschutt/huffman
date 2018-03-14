# Setup project
cmake_minimum_required(VERSION 2.8.9)

set(VERSION_MAJOR 0)
set(VERSION_MINOR 0)
set(VERSION_PATCH 1)
set(DESCRIPTION "Huffman encoding / decoding library")
set(LIBRARY_NAME huffman)

project(${LIBRARY_NAME} VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH} DESCRIPTION ${DESCRIPTION})

# Set the include directories
include_directories(include)

# Set source directories
# Globally importing files is not "recommended", but manually specifying each file is retarded
# If there is a better way to say this, please let me know
file(GLOB SOURCES "src/*.cpp")

#Generate the shared library from the sources
add_library(${LIBRARY_NAME} SHARED ${SOURCES})

#Set the location for library installation -- i.e., /usr/lib in this case
# not really necessary in this example. Use "sudo make install" to apply
install(TARGETS ${LIBRARY_NAME} DESTINATION /usr/lib)
