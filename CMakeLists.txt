cmake_minimum_required(VERSION 3.0.2)
project(yavl-cpp)

add_compile_options(-std=c++14)
set(CMAKE_BUILD_TYPE Debug)
#set(CMAKE_BUILD_TYPE Release)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O3 -fPIC")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -fPIC")

set(CATKIN_PACKAGE_DEPENDENCIES)
set(LIBRARY_NAME yavl-cpp)

find_package(catkin REQUIRED COMPONENTS ${CATKIN_PACKAGE_DEPENDENCIES})

find_package(PkgConfig REQUIRED)
pkg_check_modules(YAMLCPP yaml-cpp REQUIRED)

catkin_package(
        INCLUDE_DIRS include
        LIBRARIES ${LIBRARY_NAME}
        CATKIN_DEPENDS ${CATKIN_PACKAGE_DEPENDENCIES}
        DEPENDS YAMLCPP
)

include_directories(
        include
        ${catkin_INCLUDE_DIRS}
        ${YAMLCPP_INCLUDE_DIRS}
)

add_library(${LIBRARY_NAME} STATIC src/yavl.cpp)
target_link_libraries(${LIBRARY_NAME} ${catkin_LIBRARIES} ${YAMLCPP_LIBRARIES})
add_dependencies(${LIBRARY_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

add_executable(checker example-code/checker.cpp)
target_link_libraries(checker ${LIBRARY_NAME} ${catkin_LIBRARIES})

install(TARGETS ${LIBRARY_NAME}
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h"
)
