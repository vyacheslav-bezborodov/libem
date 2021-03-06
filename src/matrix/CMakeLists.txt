cmake_minimum_required(VERSION 2.8.5)

set(target_name matrix)

set(include_dirs)
set(link_libraries)

find_package(CUnit)
if(CUNIT_FOUND)
	set(include_dirs
		${include_dirs}
		${CUNIT_INCLUDE_DIRS}
	)
	set(link_libraries
		${link_libraries}
		${CUNIT_LIBRARIES}
	)
endif()

find_package(MPFR)
if(MPFR_FOUND)
	set(include_dirs
		${include_dirs}
		${MPFR_INCLUDES}
	)
	set(link_libraries
		${link_libraries}
		${MPFR_LIBRARIES}
	)
endif()

find_package(OpenMP)
if(OPENMP_FOUND)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
endif()

include_directories(${include_dirs})

file(GLOB src_files RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "src/*.c")

add_library(
	${target_name} SHARED
	${src_files}
)

target_link_libraries(
	${target_name} -Wl,-E
	${link_libraries}
)

if(${CMAKE_BUILD_TYPE} STREQUAL Debug)
	file(GLOB test_files RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "test/*.c")

	set(test_name ${target_name}_test)

	add_executable(
		${test_name}
		${test_files}
	)

	target_link_libraries(
		${test_name} -Wl,-E
		${target_name}
	)
endif()

