# Setup project
cmake_minimum_required(VERSION 2.8.9)

project(huffman)
set(${PROJECT_NAME}_VERSION_MAJOR 0)
set(${PROJECT_NAME}_VERSION_MINOR 0)
set(${PROJECT_NAME}_VERSION_PATCH 1)

# User can override these settings
set(LIB_INSTALL_DIR "/usr/lib")
set(HEADER_INSTALL_DIR "/usr/include")

# Globally importing files is not "recommended", but manually specifying each file is retarded
# If there is a better way to say this, please let me know
file(GLOB SOURCES "src/*.cpp")

# Set the include directories
include_directories("include")
add_library(${PROJECT_NAME} SHARED ${SOURCES})

# Install library
install(TARGETS ${PROJECT_NAME} DESTINATION "${LIB_INSTALL_DIR}/${PROJECT_NAME}")

# Install the API header
file(GLOB HEADERS "include/huffman.h")
install(FILES ${HEADERS} DESTINATION "${HEADER_INSTALL_DIR}/${PROJECT_NAME}")

