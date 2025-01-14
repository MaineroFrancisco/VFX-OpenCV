# CMakeList.txt : Top-level CMake project file
cmake_minimum_required (VERSION 3.28)

find_package(imgui REQUIRED)
find_package(OpenCV REQUIRED)

# clangd (much better than intellisense) needs the compile_commands.json in the root folder or build folder.
# For IDE setup, see https://clangd.llvm.org/installation#project-setup
# If not explicitly set, we enable it:
if (NOT DEFINED CMAKE_EXPORT_COMPILE_COMMANDS)
	set(CMAKE_EXPORT_COMPILE_COMMANDS ON CACHE BOOL "" FORCE)
	message(STATUS "CMAKE_EXPORT_COMPILE_COMMANDS not set explicitly")
endif()
message(STATUS "Using CMAKE_EXPORT_COMPILE_COMMANDS=${CMAKE_EXPORT_COMPILE_COMMANDS}")

# Use sccache if it is available on the system
find_program(SCCACHE sccache)
if(SCCACHE)
	message(STATUS "Using sccache: ${SCCACHE}")
	set(CMAKE_C_COMPILER_LAUNCHER ${SCCACHE})
	set(CMAKE_CXX_COMPILER_LAUNCHER ${SCCACHE})
	set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT Embedded)
endif()

if(WIN32)
	# We are pretty confident that everyone would prefer to define NOMINMAX on windows
	add_compile_definitions(NOMINMAX)
endif()

set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)
set(CMAKE_CXX_STANDARD 20)

project("VFX-OpenCV")

set(HEADERS )
set(SOURCES main.cpp)

add_executable(${PROJECT_NAME} ${HEADERS} ${SOURCES})
target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_23)

# TODO: Not use all opencv libs, only necessary
target_link_libraries(${PROJECT_NAME} PUBLIC imgui::imgui ${OpenCV_LIBS})