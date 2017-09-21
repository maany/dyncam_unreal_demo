# DynCam

Application to test out reactive programming in combination with depth cameras like the Microsoft Kinect v2.

## Prerequisities

* A C++14 capable compiler
* CMake

## Dependencies

* [C++React](https://github.com/schlangster/cpp.react) - A reactive programming library for C++11
* [Intel TBB](https://www.threadingbuildingblocks.org/) - Dependency for C++React
* [Qt5](https://www.qt.io/developers/) - For the GUI (soon)
* [freenect2](https://github.com/OpenKinect/libfreenect2) - For Kinect v2 support (soon)
* [glfw3](http://www.glfw.org/docs/latest/) - Dependency for freenect2
* [libturbojpeg](http://libjpeg-turbo.virtualgl.org/) - Dependency for freenect2

If you are using Linux, most of these can be installed via your distro's package manager.

## Building

This project uses cmake and can be build like other CMake-projects. However, proper find-modules for freenect2 and C++React are still missing. You have to specify the paths yourself like below (yours paths may vary):

```
$ mkdir build
$ cd build
$ cmake .. -Dfreenect2_DIR=~/Installs/lib/cmake/freenect2/ -Dcppreact_INCLUDE_DIR=~/cpp.react/include -Dcppreact_LIBRARY=~/cpp.react/build/lib/libCppReact.a
```
For future reference, find-modules should be shipped in /cmake/modules (e.g. FindCppReact.cmake and FindFreenect2.cmake).

## Building (Windows 10)
- install dependencies

    * [Visual Studio 2017](https://www.visualstudio.com/downloads/) - Make sure to install [Visual C++ Build Tools](https://blogs.msdn.microsoft.com/vcblog/2016/11/16/introducing-the-visual-studio-build-tools/) for CMake and Windows 10 SDK.
    * [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) - Required to build libusb on Windows. Libfreenect2 provides a (cmd file)[https://github.com/OpenKinect/libfreenect2/blob/master/depends/install_libusb_vs2015.cmd] which uses MS Build tools for Visual Studio 2015 to build libusb. If you want to set up libusb on your own, this step can be ignored.
    * (CMake for Windows)[https://cmake.org/download/] - Used to build the project and other dependencies
    * [Git Bash](https://git-scm.com/download/win)
    * [Boost 1.60](https://sourceforge.net/projects/boost/files/boost/1.60.0/) - A dependency for NetCam. Follow Ardian Conlon's (answer on StackOverflow)[https://stackoverflow.com/questions/2322255/64-bit-version-of-boost-for-64-bit-windows] for  instructions on how to correctly build the boost libraries for Windows. 
    * [Qt5](https://info.qt.io/download-qt-for-application-development) - Used for displaying Server GUI. Download and install like a regular windows program.
    * [OpenCV](https://sourceforge.net/projects/opencvlibrary/files/opencv-win/)
    * [Eigen3](http://eigen.tuxfamily.org/index.php?title=Main_Page) - Currently we are using (Eigen 3.3.4)(http://bitbucket.org/eigen/eigen/get/3.3.4.zip). 
        - Download the .zip file and extract the contents to a folder. 
        - Create a build directory inside top level subdirectory that contains folders like bench, cmake, Eigen etc. 
        - Open CMake and set build directory to the build folder created in last step.
        - Set the source folder to the subdirectory mentioned in second step.
        - Configure and Generate the build using Visual Studio 15 2017 Win64 generator and native compilers.
    * [tbb](https://github.com/01org/tbb/releases) - Download and extract the .zip file for Windows from the release page.
    * [libfreenect2](https://github.com/OpenKinect/libfreenect2) - Follow the instructions for building freenect2 on Windows. Use the **libusbk drivers** instead of the USBDK drivers.
        - Configure and Generate the build using Visual Studio 15 2017 Win64 generator and native compilers instead of Visual Studio 14 2015 Win64 generator as specified in libfreenect2's installation instructions.
- update environment variables
    Create the following environment variables:
    * Boost_DIR - Top level directory containing folders like boost, build etc.
    * Boost_LIB - %Boost_DIR%\stage\lib. Contains all .lib files that we built.
    * OpenCV_DIR - The path to the OpenCV *.cmake files. Location in ${folder where you installed OpenCV}\build\x64\vc14\lib
    * TBB_DIR - ${Subdirectory_of_extraction_directory_containing_cmake_folder}\cmake
    * Qt_Widgets - ${Installation Directory}\${version}\msvc2015_64\lib\cmake\Qt5Widgets
    * freenect2_DIR - ${libfreenect2_directory_downloaded_from_github}\build       
- Open a git bash. Clone this repository. Update the [cpp.react](https://github.com/Schroedi/cpp.react) submodule and create a build directory  

```
git clone https://gitlab.informatik.uni-bremen.de/cgvr/dyncam
cd ./dyncam
git submodule update
mkdir build && cd build

```
- CMake configurations
    - Open CMake and set Source Directory to dyncam's root directory 
    - Set build directory to the one we created using git bash
    - uncheck build test, example benchmark
    - Click Configure. Make sure you use Visual Studio 15 2017 Win64 generator and native compilers.
    - Click Generate and open the Visual Studio Solution.  

- Build the project called DynCam. If you see build errors related to cpp.react, then remove all the content inside ${dyncam_root_directory}/deps/cpp.react/ and replace it with those found in react-win-dyncam branch in [maany's fork of cpp.react](https://github.com/maany/cpp.react/tree/react-win-dyncam) 

## Contributing

We are using a modified version of the [Gitflow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow), where new features and fixes will be developed on separate branches (e.g. feature/xyz; fix/issue#001 etc.) and then merged back into *master*, which serves as the main development branch. Occasianlly, stable states of the programme will be merged onto the *stable* branch and tagged with a version number. The stable state must always build correctly and run without deal-breaking bugs. Features **may not** be merged directly into *stable*, only hotfixes for severe bugs may be merged directly. If a hotfix was applied, make sure to also merge it into *master*, to keep the development state up-to-date.

Note that in contrast to the regular Gitflow conecpt, *master* => *stable* and *develop* => *master*.