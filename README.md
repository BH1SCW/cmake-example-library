cmake-example-library
=====================

CMake library example that can be found using `find_package()`.

In this example, `Foo` is the library and `Bar` a binary (which uses the library).


### Features

  * The main **advantage** of this example is that it is _auto-generated_.
    You only need to change the _project name_.

  * `find_package(...)`

  * Version file: `#include <foo/version.h>`

  * `FOO_DEBUG` added on Debug. See [foo/foo.cpp](foo/foo.cpp) file.


#### Usage

Once the library is installed, it can be found externally with `find_package(...)`:

    find_package(Foo <VERSION> REQUIRED)
    target_link_libraries(... Foo::foo)

See [more details](https://github.com/pablospe/cmake-example-library/tree/moderm_cmake#how-to-use-the-library-as-dependency-in-an-external-project) below.


### How to create a library from this example?

Follow these steps:

  * Copy files in a new folder, or clone with git:

    > git clone git@github.com:pablospe/cmake-example-library.git

  * Change project name in the top-level `CMakeLists.txt`.

  * Add `.cpp` and `.h` files in `foo/CMakeLists.txt`.

  * [Optional] Set variables: `LIBRARY_NAME` and `LIBRARY_FOLDER`.
    If it is not set explicitally, project name in lowercase will be used.
    See `cmake/SetEnv.cmake` file to see the implementation details.

  * [Optional] 'example_internal/' folder can be removed, it is the 'bar' example.
    In this case, remove the 'add_subdirectory(example_internal)' too.

### How to compile?

Assume the following settings:

    project(Foo)
    ...
    set(LIBRARY_NAME foo)   # [optional] generated automatically (in lowercase)
    set(LIBRARY_FOLDER foo) # [optional] generated automatically (in lowercase)

Example of a local installation:

    > mkdir _build && cd _build
    > cmake -DCMAKE_INSTALL_PREFIX=../_install ..
    > cmake --build . --target install -j 8

Installed files:

    > tree ../_install

    ├── bin
    │   └── bar
    ├── include
    │   └── foo
    │       ├── foo.h
    │       └── version.h
    └── lib
        ├── cmake
        │   └── Foo
        │       ├── FooConfig.cmake
        │       ├── FooConfigVersion.cmake
        │       ├── FooTargets.cmake
        │       ├── FooTargets-debug.cmake
        │       └── FooTargets-release.cmake
        ├── libfoo.a                             (Release)
        └── libfood.a                            (Debug)

Uninstall library:

    > make uninstall


#### Compilation scripts

Please check the following files for more complex compilation examples:
  - [build_linux.sh](build_linux.sh)
  - [build_win.sh](build_win.sh)
  - [build_ninja.sh](build_ninja.sh)


### Static vs Shared

By default, a static library will be generated. Modify `BUILD_SHARED_LIBS` in
order to change this behavior. For example,

    > cd _build/
    > cmake -DBUILD_SHARED_LIBS=ON ..



### How to use the library (internally in subfolders)?

See the [example of internal subfolder](example_internal/).


### How to use the library (as dependency) in an external project?

See the [example of external project](example_external/).
Once the library is intalled, cmake would be able to find it using `find_package(...)` command.

    cmake_minimum_required(VERSION 3.9)
    project(Bar)

    find_package(Foo 1.2.3 REQUIRED)

    add_executable(bar bar.cpp)
    target_link_libraries(bar PRIVATE Foo::foo)

Requirements will propagate automatically:
  * `Foo::foo` will link automatically,
  * headers can be included by C++ code like `#include <foo/foo.h>`,
  * `FOO_DEBUG=1` added on Debug,
  * `FOO_DEBUG=0` added otherwise.


### How to use the library as submodule (using add_subdirectory)?

If `Foo` library is intended to be use as a Git submodule:

    > git submodule add https://github.com/<user>/Foo Foo

In the `CMakeLists.txt` where the `Foo` submodule will be used,
add the command **add_subdirectory(...)**:

    [...]

    # Add 'Foo' library as submodule
    add_subdirectory(Foo)

    # Propagate usage requirements from Foo linked library
    target_link_libraries(<target> PRIVATE Foo::foo)

    [...]


### Documentation

Some ideas from:
  * https://github.com/forexample/package-example

Modern CMake tutorials (youtube):
  * C++Now 2017: Daniel Pfeifer [Effective CMake](https://www.youtube.com/watch?v=bsXLMQ6WgI)
  * CppCon 2018: Mateusz Pusz [Git, CMake, Conan - How to ship and reuse our C++ projects](https://www.youtube.com/watch?v=S4QSKLXdTtA)