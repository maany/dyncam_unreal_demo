cmake_minimum_required(VERSION 3.1)

project(DynCam)

if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS "6.1")
        message(FATAL_ERROR "Insufficient gcc version")
    endif()
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS "15")
        message(FATAL_ERROR "Insufficient msvc version")
    endif()
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
	if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS "3.4")
        message(FATAL_ERROR "Insufficient clang version")
    endif()
endif()

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
	### compiler settings
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wpedantic -Werror")
	# cpp.react uses old boost coroutines. we want to ignore this warning
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DBOOST_COROUTINES_NO_DEPRECATION_WARNING")
	# library warings we don't care about
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-gnu -Wno-dangling-field -Wno-mismatched-tags")
endif()
### Required modules that are not installed on the system
list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/modules")

### dependencies
if(WIN32)
	#TODO Chris TBB 
	list(APPEND CMAKE_MODULE_PATH "C:/Users/cgvr/Documents/MultiUserVR/tbb/cmake")
endif()
add_subdirectory(deps/cpp.react)
find_package(OpenCV REQUIRED)
if(WIN32)
	find_package(TBB REQUIRED tbb)
	include_directories("${TBB_DIR}/../include")
	#include_directories("C:/Users/cgvr/Documents/MultiUserVR/opencv/build/include/")
	#include_directories("${freenect2_INCLUDE_DIR}")
endif()

### assemble our targets
add_subdirectory(src/Cams)

# main library
add_library(libdyncam src/ReactDomain.h src/libdyncam.cpp)

set_property(TARGET libdyncam PROPERTY CXX_STANDARD_REQUIRED ON)
set_property(TARGET libdyncam PROPERTY CXX_STANDARD 14)
target_include_directories(libdyncam PUBLIC 
		${OpenCV_INCLUDE_DIRS}
        deps/cpp.react/include)


target_link_libraries(libdyncam
        CppReact
        ${OpenCV_LIBS}
	    libcams)

option(enable_tracking "Build tracking support" ON)
if(enable_tracking)
	add_subdirectory(src/Tracker)
	target_link_libraries(libdyncam
			tracking)
endif()


### reconstruction part
add_subdirectory(src/Reconstruction)
target_link_libraries(libdyncam
		libReconstruct)

# applications
option(enable_ui "Build ui tools" ON)
if(enable_ui)
	add_subdirectory(src/UI)
endif()
# main test app
add_executable(DynCam src/Main.cpp)
set_property(TARGET DynCam PROPERTY CXX_STANDARD_REQUIRED ON)
set_property(TARGET DynCam PROPERTY CXX_STANDARD 14)
target_include_directories(DynCam SYSTEM PRIVATE
        deps/cpp.react/include)
target_link_libraries(DynCam
		libdyncam)

# calibration test app
add_executable(Calibration src/Calibration.cpp)
set_property(TARGET Calibration PROPERTY CXX_STANDARD_REQUIRED ON)
set_property(TARGET Calibration PROPERTY CXX_STANDARD 14)
target_include_directories(Calibration SYSTEM PRIVATE
	    deps/cpp.react/include)
target_link_libraries(Calibration
	    libdyncam)

# unreal wrapper
add_library(libDyncamUnreal SHARED
	src/DynCamUE4Wrapper.cpp
	src/DynCamUE4Wrapper.h)
target_link_libraries(libDyncamUnreal libdyncam)

