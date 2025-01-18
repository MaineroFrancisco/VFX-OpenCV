# CMakeList.txt : Top-level CMake project file
cmake_minimum_required (VERSION 3.28)

project("VFX-OpenCV")

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
set(CMAKE_CXX_STANDARD 23)

if(MSVC)
add_compile_options(
	$<$<CONFIG:>:/MT> #---------|
	$<$<CONFIG:Debug>:/MTd> #---|-- Statically link the runtime libraries
	$<$<CONFIG:Release>:/MT> #--|
)
endif()

#find_package(OpenGL REQUIRED)
#find_package(glfw3 REQUIRED)
#set(GLEW_USE_STATIC_LIBS ON)
#find_package(GLEW REQUIRED)
#find_package(imgui REQUIRED)
message(${CMAKE_PREFIX_PATH})

set(OpenCV_STATIC ON)
find_package(OpenCV REQUIRED)

set(HEADERS )
set(SOURCES main.cpp)

add_executable(${PROJECT_NAME} ${HEADERS} ${SOURCES})
#target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_23)

# TODO: Not use all opencv libs, only necessary
#target_link_libraries(${PROJECT_NAME} PRIVATE glfw GLEW::glew OpenGL::GL imgui::imgui ${OpenCV_LIBS})
target_link_libraries(${PROJECT_NAME} PRIVATE ${OpenCV_LIBS})

# Compile definition for assets folder
target_compile_definitions(${PROJECT_NAME} PUBLIC ASSETS_FOLDER="${CMAKE_CURRENT_SOURCE_DIR}/assets/")