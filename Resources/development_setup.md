**This installation guide is currently only for MacOS.**
**If you are using a Windows, there is still important information below the installation section.**

**For MacOS:**

- Install homebrew
- MacOS requires **Xcode Command Line Tools** for compiling C++ projects.
- ensure you have it installed:
  - xcode-select --install
- Install CMake:
  - brew install cmake
  - optional: install ninja
    - it just makes compilation faster.
- clone JUCE:
  - git clone --recurse-submodules https://github.com/juce-framework/JUCE.git ~/JUCE
    - this installs it to your user directory via ~/JUCE but you can install wherever you want. I recommend just installing to your user directory.
- Install CLion by JETBRAINS
  - Apply for free version through the Education tab
  - Use your school email to apply
  - Takes a couple of minutes to get accepted
- Open CLion and create a **temporary test project**:
  - New project → Select CMake or C++ if CMake is not an option.
  - This will generate a **CMakeLists.txt** file automatically.
  - **This step is just to get familiar with CMake and CLion.**
  - You can just delete this project later when cloning GrooveKit.
- edit your CMakeLists.txt
  - Copy this:
```
cmake_minimum_required(VERSION 3.15)
project(midi VERSION 1.0)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# this is the path to your JUCE directory set to your actual path. 
set(JUCE_DIR /Users/username/JUCE)

# adds JUCE as a subdirectory.
add_subdirectory(${JUCE_DIR} JUCE)

# creates the Executables.
# CLion should create this for you as you add files.
# but make sure this section has your file names.
add_executable(midi
    src/main.cpp
    src/SomeFileName.h
    src/SomeFileName.cpp
)

# this is where you link your libraries such as JUCE.
# i've added an example juce library.
target_link_libraries(midi PRIVATE
    juce::juce_gui_basics
)
```
## **!!! IMPORTANT !!!**

After you have gotten your project to build and run on your local machine
we need to make a change to be able use JUCE in our git repo.

**Issue:** 

Currently, our MakeLists.txt file includes this line:
```set(JUCE_DIR /Users/username/JUCE)```
this is a path to JUCE specific to your system.
when someone else clones the repo, JUCE will likely be in a different location.

**Fix:** 
- clone the GrooveKit repo 
- there is a directory in the repo called cmake
- inside that directory create a file called JUCEPath.cmake
- add this line to that file:
- ```set(JUCE_DIR "/path/to/JUCE" CACHE PATH "Path to JUCE" FORCE)```
  - change the path to wherever your JUCE directory is located.
- now in our repo's CMakeLists.txt file instead of: 
- ```set(JUCE_DIR /Users/username/JUCE)```
- which is what we used in our test project.
- we have:
```
if (EXISTS "${CMAKE_SOURCE_DIR}/cmake/JUCEPath.cmake")
    include("${CMAKE_SOURCE_DIR}/cmake/JUCEPath.cmake")
else()
    message(FATAL_ERROR "JUCEPath.cmake not found. Please create it and specify your JUCE directory.")
endif()
```
- this just makes sure you have the JUCEPath.cmake and then includes it.
- the JUCEPath.cmake is ignored in the git ignore
- so my JUCEPath.cmake will always be different from yours and vice versa
- make sure it builds and runs!
  - let me know if you have issues.
- Another note:
  - IDE's create build directories, for instance CLion creates a directory called cmake-build-debug.
  - I have added the possible build directories that I found in the .gitignore file.
  - before you push, check to make sure your build directory is being ignored.
  - otherwise, our individual build setups could change and make things messy. 
  - this way we can all have our own build setup independent of the git repo.
