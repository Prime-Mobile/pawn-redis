cmake_minimum_required(VERSION 3.15)
project(pawn-redis)

cmake_policy(SET CMP0091 NEW)

list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/lib/cmake-modules")

if (MSVC)

	add_link_options(/INCREMENTAL:NO /NODEFAULTLIB:MSVCRT)
	# link runtime statically
	set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>DLL")
	set(MSVC_RUNTIME_LIBRARY_CONFIG "/MD")

elseif (UNIX)

	add_compile_options(
		-m32
		-fvisibility=hidden
		# Possibly one of them can be enabled: -std=gnu++98, -std=c++14
		# $<$<CXX_COMPILER_ID:GNU>:-std=c++14>
		# TODO: Uncomment to enable -pedantic
		#$<$<CXX_COMPILER_ID:GNU>:-pedantic>
	)

	set_property(GLOBAL PROPERTY FIND_LIBRARY_USE_LIB64_PATHS OFF)

	add_link_options(
		"-static-libgcc"
		"-static-libstdc++"
	)

	set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,-rpath='$ORIGIN'")

endif()

# Removes the sprintf warnings from plugin-natives
add_compile_definitions(_CRT_SECURE_NO_WARNINGS)

# Output
set(CMAKE_BINARY_DIR ${CMAKE_SOURCE_DIR}/build/plugins)
set(LIBRARY_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/build/plugins)

set(CMAKE_BUILD_TYPE Debug)
set(BUILD_SHARED_LIBS OFF)
set(LOGGING_ENABLED OFF)
set(BUILD_EXAMPLES OFF)
set(BUILD_TESTS OFF)

include_directories(
	lib/cpp_redis/tacopie/includes
	lib/cpp_redis/includes
)

add_subdirectory(lib/cpp_redis)
add_subdirectory(src)
