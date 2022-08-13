cmake_minimum_required(VERSION 3.6 FATAL_ERROR)
project(toy_lang CXX)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
option (FORCE_COLORED_OUTPUT "Always produce ANSI-colored output (Clang only)." TRUE)
if (${FORCE_COLORED_OUTPUT})
    if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
       add_compile_options (-fdiagnostics-color)
    endif()
endif()

#set(CMAKE_CXX_CLANG_TIDY
#	clang-tidy;
#	-checks=-*,cppcoreguidelines-*,bugprone-*,performance-*,modernize-*,analyze-*,-modernize-use-trailing-return-type*,-cppcoreguidelines-avoid-magic-numbers*;
#	-p=std=c++20;
#	--use-color)

#add_subdirectory(external/utf8proc)

include(FetchContent)
FetchContent_Declare(
    utf8proc
    GIT_REPOSITORY https://github.com/JuliaStrings/utf8proc.git
    GIT_TAG master
)
FetchContent_MakeAvailable(utf8proc)

# Set directory paths
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/source)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

set(sources
	${SOURCE_DIR}/util.cpp
	${SOURCE_DIR}/eval.cpp
	${SOURCE_DIR}/lexer.cpp
	${SOURCE_DIR}/file.cpp
	${SOURCE_DIR}/highlight.cpp
	${SOURCE_DIR}/parser.cpp
	${SOURCE_DIR}/repl.cpp
	${SOURCE_DIR}/main.cpp
)
add_executable(${PROJECT_NAME} ${sources})
target_include_directories(${PROJECT_NAME} PUBLIC ${SOURCE_DIR}/include ${CMAKE_CURRENT_SOURCE_DIR}/external ${CMAKE_CURRENT_SOURCE_DIR}/external/utf8proc)
target_link_libraries(${PROJECT_NAME} PUBLIC utf8proc)
target_compile_options(${PROJECT_NAME} PRIVATE
  $<$<CXX_COMPILER_ID:MSVC>:/W4>
  $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra -pedantic -Wno-missing-field-initializers -Wno-unused-parameter -g -std=c++20>
)

# Enable C++20
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_20)
