cmake_minimum_required(VERSION 3.1)
project(clear)

set(CMAKE_CXX_STANDARD 11)


# Find Eigen3
message(STATUS "CMAKE_SOURCE_DIR: ${CMAKE_SOURCE_DIR}")
find_package(Eigen3 REQUIRED)
if(Eigen3_FOUND)
message(STATUS "Found Eigen3, version: ${EIGEN3_VERSION_STRING}")
message(STATUS "Eigen3 include directory: ${EIGEN3_INCLUDE_DIR}\n")
else()
message(FATAL_ERROR "Could not find Eigen!")
endif()

include_directories(${PROJECT_SOURCE_DIR}/include
  ${EIGEN3_INCLUDE_DIR})
message(${PROJECT_SOURCE_DIR})


file(GLOB CLEAR_HPP_FILESYSTEM "${CMAKE_CURRENT_SOURCE_DIR}/include/clear/*.hpp" )
file(GLOB CLEAR_CPP_FILESYSTEM "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp" )

add_library(openMVG_clear SHARED  
	"${CMAKE_CURRENT_SOURCE_DIR}/src/MultiwayMatcher.cpp"
	"${CMAKE_CURRENT_SOURCE_DIR}/src/MyGraph.cpp"
	"${CMAKE_CURRENT_SOURCE_DIR}/src/Hungarian.cpp"
	${CLEAR_HPP_FILESYSTEM})

INSTALL(TARGETS openMVG_clear
	EXPORT clear-targets
	PUBLIC_HEADER DESTINATION include
	ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        )
INSTALL(EXPORT clear-targets FILE clear-config.cmake DESTINATION lib/cmake/clear)
INSTALL(DIRECTORY include/clear
        DESTINATION include
        )


add_executable(clear_tests
        src/tests/clearUnitTests.cpp)
target_include_directories(clear_tests PRIVATE external)
target_link_libraries(clear_tests openMVG_clear)
set_property(TARGET openMVG_clear PROPERTY FOLDER OpenMVG/3rdParty/clear)
