cmake_minimum_required(VERSION 3.20)
project(PROJECT_NAME CXX)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

list(APPEND CMAKE_PREFIX_PATH "${CMAKE_SOURCE_DIR}/vcpkg_installed/${VCPKG_TARGET_TRIPLET}")

file(GLOB_RECURSE CORE_SOURCES CONFIGURE_DEPENDS *.cpp)
list(FILTER CORE_SOURCES EXCLUDE REGEX ".*/CMakeFiles/.*")
list(FILTER CORE_SOURCES EXCLUDE REGEX ".*/tests/.*")

file(GLOB_RECURSE CORE_HEADERS CONFIGURE_DEPENDS *.hpp)
list(FILTER CORE_HEADERS EXCLUDE REGEX ".*/CMakeFiles/.*")
list(FILTER CORE_HEADERS EXCLUDE REGEX ".*/tests/.*")

set(INCLUDE_DIRS "")
foreach(HEADER ${CORE_HEADERS})
    get_filename_component(HEADER_DIR ${HEADER} DIRECTORY)
    get_filename_component(PARENT_DIR ${HEADER_DIR} DIRECTORY)
    list(APPEND INCLUDE_DIRS ${HEADER_DIR})
    list(APPEND INCLUDE_DIRS ${PARENT_DIR})
endforeach()
list(REMOVE_DUPLICATES INCLUDE_DIRS)

add_library(${PROJECT_NAME}_core OBJECT ${CORE_SOURCES})
target_include_directories(${PROJECT_NAME}_core PUBLIC ${INCLUDE_DIRS})

add_executable(${PROJECT_NAME} $<TARGET_OBJECTS:${PROJECT_NAME}_core>)
target_include_directories(${PROJECT_NAME} PRIVATE ${INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} PRIVATE ${PROJECT_NAME}_core)

enable_testing()
add_subdirectory(tests)
