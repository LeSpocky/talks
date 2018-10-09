# CMake Talk

## documentation

* see
  * https://cmake.org/documentation
  * https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#introduction
  * https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#organization
* look out for „Modern CMake“
* beware of plenty of example showing a wrong (mostly old) way to do things

## out of tree builds

* essential for putting your source code into version control
* make one build directory per compiler

### usual way

    ~/path/to/your/src $ mkdir build
    ~/path/to/your/src $ cd build
    ~/path/to/your/src/build $ cmake ..
    ~/path/to/your/src/build $ make

* first call to `cmake` is special, sets generator and compiler
* pre-set options with `-D`, you can overwrite that later, but maybe useful
  when building from some external build system (like buildroot, ptxdist, …)

## gui

* try it, may suit you
* nice for choosing a compiler
* pick files and paths easily
* alternative:
  * Selecting a compiler must be done on the first run in an empty directory.
  * `~/package/build $ CC=clang CXX=clang++ cmake ..`

## generators

* visual studio, ninja, eclipse, …
* gui lets you pick
* list with `cmake --help`
* CMake will generate useful make targets for you

## syntax

* very first command in every top level CMakeLists.txt is like this:

    cmake_minimum_required(VERSION 3.1)

* second command in your top level CMakeLists.txt is like that:

    project(MyProject
        VERSION 1.0
        DESCRIPTION "Very nice project"
        LANGUAGES CXX
    )

* CMake commands are documented
* syntax itself is quite straight forward
  * whitespace does not matter
  * comments start with '#' and go till end of line
  * avoid comments inside function call parenthesis

## targets

### define them

* target for a simple executable (you don't need to add header files here)

    add_executable(one two.cpp three.h)

* target name is 'one'
* build library as simple:

    add_library(one STATIC two.cpp three.h)

* or omit STATIC and let `BUILD_SHARED_LIBS` decide
* also INTERFACE and ALIAS targets

### add information needed for build

* `target_include_directories()`
  * PUBLIC, PRIVATE, INTERFACE
* `target_link_libraries()`
  * THIS is the most powerful and central command
* targets can have more: properties, compile options, compile definitions,
  compile features, …
  * the latter are just properties with special meanings
* see all the `target_*()` functions

### export and import them

* export your library targets, so others can use your library
* use imported targets (`find_package()`) to have that ready for `target_link_libraries()`

### chain targets

* use target names as argument for `target_link_libraries()`
  * make sure what you provide as target name is actually a target,
    best way is to use namespaces (see ALIAS targets)
  * use PUBLIC, PRIVATE and INTERFACE to not propagate non necessary dependencies
* possible are all kinds of targets
* this creates some kind of dependency tree
* CMake takes care of building things in the right order

## variables and properties

* `set()` sets variables
* use `ALL_CAPS` for names and `${MY_VARIABLE}` for access
* beware of spaces, if not quoted, that can create lists
* always enclose paths in spaces: `"${MY_PATH}"`
* properties are stored pieces of information attached to directories, targets,
  tests, source files, cache entries, and more
* see https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html

### cache

* cached variables can be listed and manipulated in GUI
* command `option()` is some syntactic sugar for cached boolean variables
* the cache is a text file in the build directory

### useful options

* see https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html
* `CMAKE_BUILD_TYPE`
  * *Debug*: no optimization, debug symbols, useful together with gdb
  * *Release*: full optimization, no debug symbols
  * *RelWithDebInfo*: optimization _and_ debug symbols
  * CMake knows the compilers, sets matching options
* `CMAKE_INSTALL_PREFIX`
  * defaults to `/usr/local`, like `--prefix` (?) in autotools
  * important when building for distribution
* `BUILD_SHARED_LIBS`
  * global CMake variable to control if static or shared libraries are built

## generate things to be built

* use `configure_file()`
* for example create a `version.h` from a `version.h.in` and substitute
  placeholders with contents of CMake variables
* set preprocessor switches from CMake options
* same mechanism is used for so called “CMake package files” used to export
  library targets
* more complex things involve custom targets, and custom commands

## cross compiling

* toolchain files

## Programming and extending

* `if()` is very powerful
* you can define your own custom targets, functions, and macros (e.g. to
  create modules)
* of course you can call external tools (different depending on configure
  or build time)

## Minimum CMake version

* Use at least 3.1
* Increase if you need special things, like from certain modules
* Setting to latest stable usually not necessary
  (will annoy people who want to just use CMake coming with the distribution)
* you might want to make yourself familiar with CMake policies

## Best practices

* define everything per target
* treat your CMakeLists.txt as code, readable and well documented
* think in targets
* export your targets
* make alias targets to make use of `add_subdirectory` and `find_package` consistent
* lower case functions names

## Antipatterns

* Do not use global functions like `link_directories` or `include_libraries`
* Do not set compiler flags in your CMakeLists.txt files!
  * CMake knows different compilers in different versions and how to set,
    what you want to achieve
  * e.g. if you want C++11 support, there are
    * meta compiler features (CMake 3.8+)
    * compiler features (CMake 3.1+)
    * per target properties (`CXX_STANDARD`, …)

## Related tools

* CTest
* CDash
* CPack

## CMake Changelog

### v3.0

* INTERFACE libraries
* Project VERSION support

### v3.1

* C++11 Support
* Compile features support
* Sources can be added later with target_sources

### v3.3

* `IN_LIST`
* `INCLUDE_WHAT_YOU_USE`

### v3.4

* compiler launcher??
* first find modules use namespaces and imported targets

### v3.6

* clang-tidy
* standard include dirs and standard libs for toolchains

### v3.7

* `LINK_WHAT_YOU_USE`

### v3.8

* C# and CUDE as language

### v3.10

* cppcheck

### v3.11

* much faster

