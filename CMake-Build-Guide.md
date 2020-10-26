## Windows
Long term users will notice that the `win32` folder, the `.sln` file, and `.vcproj` files are all gone. In their place: `CMakeLists.txt` files in every folder that has something to be built. Don't worry, `Visual Studio 2017` and `Visual Studio 2019` have `CMake` built-in.

To open the project, follow these steps and open the `CMakeLists.txt` file in the root of the repo.

`File > Open > Folder` in the repo root will also work. 

![vs_build_1](https://user-images.githubusercontent.com/1389729/96963902-2b00cf00-1512-11eb-804c-47ea881888b9.png)
![image](https://user-images.githubusercontent.com/1389729/97025486-cd47a380-1560-11eb-8d02-0d19b539bd2c.png)

Visual Studio will think for a moment but will handle everything from here. You'll be able to select your target to build and debug as you did before. Debug paths, executable output paths etc. are all handled automatically.

## Windows Commandline
Make sure you have `CMake` available to use in your `PATH` through some means.
```
mkdir build
cd build
cmake .. && cmake --build .
```

The default is x64, you can force 32-bit with: `cmake .. -A Win32 && cmake --build .`

## Linux
The same CMake steps as before (make sure you have CMake installed with `sudo apt install cmake` or similar):
```
mkdir build
cd build
cmake ..
make -j $(nproc)
```

## Troubleshooting
On Windows, if you have versions of our external libraries installed on your machine, CMake might try to use them. You'll be able to catch this during configuration when CMake reports:
```
-- MYSQL_LIBRARY: C:\mysql-ver-1.0\lib
-- MYSQL_INCLUDE_DIR: C:\mysql-ver-1.0\include
```
This should be reporting the bundled versions we keep, something like this:
```
-- MYSQL_LIBRARY: C:\dev\topaz\ext\lib\mysql
-- MYSQL_INCLUDE_DIR: C:\dev\topaz\ext\include\mysql
```
If this happens, you can override these paths when you configure CMake:
```
cmake .. -DMYSQL_INCLUDE_DIR=C:\dev\topaz\ext\lib\mysql -DMYSQL_LIBRARY=C:\dev\topaz\ext\lib\libmariadb.lib
```

## Why the change?
Historically, we've had 3 build systems: VS Solution, Linux makefile and CMake. On all platforms, when you wanted to add a file you had to add them to the VS `.sln` file, often by hand. This made cross-platform development annoying, and merge conflicts on the XML-based `.sln` files were a pain. `CMake` files are simple and allow us to more easily split the project into logical chunks to build and apply different build settings to.

This first version is recreating the original build as closely as possible. Now that it's complete, it will allow us to:
- Make sure build flags and warning/error settings are the same on platforms.
- Add/remove/upgrade external dependencies more easily.
- Apply tools like `UBSan` and `Valgrind` to the build more easily.
- Easily apply performance benchmarking tools like `Orbit` or `Tracy`.
- Add/split-out new executables more easily, reusing existing code.

## Adding Files
Add the `.h` and the `.cpp` file to the `SOURCES` list in the `CMakeLists.txt` file in the folder where the new files live.

![image](https://user-images.githubusercontent.com/1389729/96964877-e7a76000-1513-11eb-9757-710e53b2858a.png)

If you use `Visual Studio` or `CLion`, they will automatically pick up this change and regenerate your CMake cache. On Linux you will need to update your cache by running `cmake ..` in your `build` folder before you run `make`.