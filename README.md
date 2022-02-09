# Augment-Clang
## About
This tool is designed to assist in the construction of large binary datasets in binary research work, which will be suitable for the following purposes：

1. Customize optimization level.
1. Store the object files.
1. Emit source to object path mapping
## Build
Download and compile the LLVM project first:

1. The `~/LLVM` folder will store the `llvm` repo.
1. The `~/Auto_LLVM` folder will be the output dir of `llvm` compilation.
```
mkdir ~/LLVM && cd ~/LLVM
git clone https://github.com/llvm-mirror/llvm.git
cd llvm

cd tools
git clone https://github.com/llvm-mirror/clang.git
cd ..
mkdir build && cd build

mkdir ~/Auto_LLVM
cd ~/LLVM/llvm/build
cmake -DLLVM_TARGETS_TO_BUILD=host -DCMAKE_INSTALL_PREFIX=~/Auto_LLVM -DCMAKE_BUILD_TYPE=MinSizeRel -DLLVM_EXPERIMENTAL_TARGETS_TO_BUILD=WebAssembly -DLLVM_INCLUDE_EXAMPLES=OFF -DLLVM_INCLUDE_TESTS=OFF -DCLANG_INCLUDE_TESTS=OFF ..

cmake --build . --target install -- -j24
```
Clone this repo and apply the patch files after the above steps are successfully executed.
```
cd ~
git clone https://github.com/P1umer/Augment-Clang.git

cd ~/LLVM/llvm
patch -p1 < ~/Augment-Clang/llvm/llvm.patch
cd ~/LLVM/llvm/tools/clang
patch -p1 < ~/Augment-Clang/llvm/tools/clang/clang.patch
```
Rebuild it.
```
cd ~/LLVM/llvm/build
cmake --build . --target install -- -j24
```
If rebuild fails, check the two `REVISON` files in this repo which store hash versions of `llvm` and `clang` respectively.
## Usage
Augment-clang is easy to use. There are just two environment variables you have to set.
### 1. AUTO_COMPILE_OPTION
The value of this variable will OVERWRITE the original optimization level. For example:
```
export AUTO_COMPILE_OPTION="-O1"
```
Optional value: `["-O0","-O1","-O2","-O3","-O4","-Os","-Oz","-Ofast"]`.
### 2. AUTO_COMPILE_STORAGE_PATH
This environment variable should be set to the global path to the folder where object file copies are stored.
```
export AUTO_COMPILE_STORAGE_PATH="/tmp/copys"
```
After the compilation is completed, an additional `BIN2SRC` file that stores the sourcecode to object file path mapping info will also be generated in this folder.
### 3. Use Augment-Clang
The last step you need to do is to replace the system installed `clang` with `Augment-Clang`.
