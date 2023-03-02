# Custom CMake build for GA-SDK-CPP

Because their own build infrastructure is a smoking pile of rubble.

## build instructions

### Windows (x86_64)

```
mkdir build && cd build
cmake -G Ninja -DCMAKE_BUILD_TYPE=Release ..
cmake --build .
```

### MacOS (universal binary with x86_64 and arm64)

```
mkdir build && cd build
cmake -G Ninja -DCMAKE_OSX_DEPLOYMENT_TARGET=10.14 -DCMAKE_OSX_ARCHITECTURES="x86_64;arm64" -DCMAKE_BUILD_TYPE=Release ..
cmake --build .
```
