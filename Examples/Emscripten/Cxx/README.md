# Building WebAssembly examples with rendering

This example aims to provide a base example on how to create an example using
VTK for its rendering while running the code inside a browser using WebAssembly.

This example was contributed by Rostyslav Lyulinetskyy and Ilya Volkov from
https://about.dicehub.com/.

## Compiling VTK for Emscripten

```
mkdir -p work/build-vtk
cd work

git clone https://gitlab.kitware.com/vtk/vtk.git src
cd src
git submodule update --init --recursive
```

Start docker inside that working directory

```
docker run --rm --entrypoint /bin/bash -v $PWD:/work -it dockcross/web-wasm:20200416-a6b6635

cd /work/build-vtk

cmake \
  -G Ninja \
  -DCMAKE_TOOLCHAIN_FILE=${CMAKE_TOOLCHAIN_FILE} \
  -DBUILD_SHARED_LIBS:BOOL=OFF \
  -DCMAKE_BUILD_TYPE:STRING=Release \
  -DVTK_ENABLE_LOGGING:BOOL=OFF \
  -DVTK_ENABLE_WRAPPING:BOOL=OFF \
  -DVTK_LEGACY_REMOVE:BOOL=ON \
  -DVTK_MODULE_ENABLE_VTK_AcceleratorsVTKm:STRING=NO \
  -DVTK_MODULE_ENABLE_VTK_IOH5part:STRING=NO \
  -DVTK_MODULE_ENABLE_VTK_RenderingContext2D:STRING=NO \
  -DVTK_MODULE_ENABLE_VTK_ViewsContext2D:STRING=NO \
  -DVTK_MODULE_ENABLE_VTK_WebGLExporter:STRING=NO \
  -DVTK_MODULE_ENABLE_VTK_h5part:STRING=NO \
  -DVTK_MODULE_ENABLE_VTK_hdf5:STRING=NO \
  -DVTK_OPENGL_USE_GLES:BOOL=ON \
  -DVTK_USE_SDL2:BOOL=ON \
  -DVTK_NO_PLATFORM_SOCKETS:BOOL=ON \
  -DVTK_BUILD_EXAMPLES:BOOL=ON \
  /work/src

cmake --build .
```

## Compiling all Emscripten examples

```
docker run --rm --entrypoint /bin/bash -v $PWD:/work -it dockcross/web-wasm:20200416-a6b6635

mkdir -p /work/build-all-examples
cd /work/build-all-examples

cmake \
  -G Ninja \
  -DCMAKE_TOOLCHAIN_FILE=${CMAKE_TOOLCHAIN_FILE} \
  -DVTK_DIR=/work/build-vtk \
  -DOPTIMIZE=BEST \
  /work/src/Examples

cmake --build .
```

## Serve and test generated code

```
cd work/all-examples/Emscripten/Cxx/
python3 -m http.server 8000
```

Open your browser to:

- http://localhost:8000/Cone
- http://localhost:8000/ConeFullScreen
