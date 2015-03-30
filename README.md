# googlemock

This is my personal mirror of googlemock. The official source can be found at
the [googlemock website](https://code.google.com/p/googlemock/). Nothing has
been changed compared to the official download other than this README.md file.

This mirror is used to easily put googlemock in my own projects as a git
subrepo, instead of manually copying the source everywhere. To do this cleanly,
I set up an `External` directory in my projects, similar to the one outlined in
the CMakeEnvironment example in the [Cross Platform Test Driven Development
Environment Using
CMake](http://www.gamedev.net/page/resources/_/technical/general-programming/cross-platform-test-driven-development-environment-using-cmake-part-1-r2986)
tutorial.

In my projects, the `External` folder has the following structure:

    External
    ├── CMake
    │   ├── [Other libraries]
    │   └── gmock
    │       └── CMakeLists.txt
    ├── CMakeLists.txt
    └── gmock [Everything from this repo as a subrepo]

Within the `External` directory, the CMakeLists.txt files look like this:

`External/CMakeLists.txt`:

    ADD_SUBDIRECTORY(External/CMake/gmock)

`External/CMake/gmock/CMakeLists.txt`: 

    # Use a variable to point to the root of googlemock within the External
    # directory. This makes it easier to update to new releases as desired.
    SET(GOOGLEMOCK_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/../../gmock")
    INCLUDE_DIRECTORIES(${GOOGLEMOCK_ROOT}/fused-src)
    # Make the file lists.
    SET(INCLUDE_FILES ${GOOGLEMOCK_ROOT}/fused-src/gtest/gtest.h
        ${GOOGLEMOCK_ROOT}/fused-src/gmock/gmock.h)
    SET(SOURCE_FILES ${GOOGLEMOCK_ROOT}/fused-src/gmock-gtest-all.cc
        ${GOOGLEMOCK_ROOT}/fused-src/gmock_main.cc)

    # Add as a static library target.
    ADD_LIBRARY(gmock STATIC ${INCLUDE_FILES} ${SOURCE_FILES})

    SET(GMOCK_INCLUDE_DIRS ${GOOGLEMOCK_ROOT}/fused-src PARENT_SCOPE)

In the main CMakeLists.txt I add:

    ...
    INCLUDE(External/CMakeLists.txt)
    ...

In the CMakeLists.txt that is used to build the tests named MyTests, I add:
    
    ...
    TARGET_LINK_LIBRARIES(MyTests gmock)
    INCLUDE_DIRECTORIES(${GMOCK_INCLUDE_DIRS})
    ...
    
Build the project and I'm off to the races!
