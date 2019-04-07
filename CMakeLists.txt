cmake_minimum_required(VERSION 2.8)
set(Boost_USE_MULTITHREADED ON)
#set(Boost_DEBUG 1) 
find_package(Boost COMPONENTS system filesystem thread date_time chrono regex )

include_directories( ${Boost_INCLUDE_DIRS} )

IF (MSVC)
	add_definitions( "/W3 /D_CRT_SECURE_NO_WARNINGS /wd4996 /wd4345  /nologo /D_WIN32_WINNT=0x0600 /DWIN32_LEAN_AND_MEAN /bigobj" )
ELSE()
	# set stuff for other systems
	SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wno-reorder -Wunused-variable -D_GNU_SOURCE")
ENDIF()

#fix the problem "could not find library -lrt" in osx
IF(APPLE)
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DNORT")
ENDIF()

message("CMAKE_CXX_FLAGS is ${CMAKE_CXX_FLAGS} ")

include_directories(include)
include_directories(tests)
include_directories(demo)
include_directories(demp/iface)

# Add folders to filters
file(GLOB_RECURSE LEVIN_GENERAL_SECTION       RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
					     ${CMAKE_CURRENT_SOURCE_DIR}/demo/demo_levin_server/*.h
					     ${CMAKE_CURRENT_SOURCE_DIR}/demo/demo_levin_server/*.inl
					     ${CMAKE_CURRENT_SOURCE_DIR}/demo/demo_levin_server/*.cpp)

file(GLOB_RECURSE HTTP_GENERAL_SECTION       RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
					     ${CMAKE_CURRENT_SOURCE_DIR}/demo/demo_http_server/*.h
					     ${CMAKE_CURRENT_SOURCE_DIR}/demo/demo_http_server/*.inl
					     ${CMAKE_CURRENT_SOURCE_DIR}/demo/demo_http_server/*.cpp)

source_group(general  FILES ${LEVIN_GENERAL_SECTION} FILES ${HTTP_GENERAL_SECTION})
#source_group(general  FILES ${HTTP_GENERAL_SECTION})

add_executable(demo_http_server ${HTTP_GENERAL_SECTION})
add_executable(demo_levin_server ${LEVIN_GENERAL_SECTION})

target_link_libraries(demo_http_server ${Boost_LIBRARIES})
target_link_libraries(demo_levin_server ${Boost_LIBRARIES})

IF (NOT WIN32)
    IF(NOT APPLE)
        message("os not win or apple link library rt")
        target_link_libraries (demo_http_server rt)
        target_link_libraries (demo_levin_server rt)
    ENDIF()
ENDIF()


