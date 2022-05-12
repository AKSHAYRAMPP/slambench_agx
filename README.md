# SLAMBench_jetson xavier agx
SLAMBench(updated repo) is used here for benchmarking ORBSLAM2, ORBSLAM3 on NVIDIA Jetson Xavier agx

Hardware Specification:

GPU:	512-core Volta GPU with Tensor Cores

CPU:	8-core ARM v8.2 64-bit CPU, 8MB L2 + 4MB L3

Memory:	32GB 256-Bit LPDDR4x | 137GB/s

Storage:	32GB eMMC 5.1 

OS: UBUNTU 18.04

# Steps for building SLAMBench on Jetson Xavier AGX

Install library Dependencies

`sudo apt-get -y install libvtk6.3 libvtk6-dev unzip libflann-dev wget
mercurial git gcc g++ cmake python-numpy freeglut3 freeglut3-dev
libglew-dev libglu1-mesa libglu1-mesa-dev libgl1-mesa-glx
libgl1-mesa-dev libxmu-dev libxi-dev libboost-all-dev cvs
libgoogle-glog-dev libatlas-base-dev gfortran gtk2.0 libgtk2.0-dev
libyaml-dev build-essential libyaml-cpp-dev`

`git clone https://github.com/pamela-project/slambench `

`cd slambench`

`make deps`

`make slambench`

This step is only required for original repo: 

{ Edit **benchmarks.makefile** inside **/slambench/framework/makefiles** and add 

```jsx
@echo -n "  - ORBSLAM3 [Campos et al, ARXIV'20] : " ; if [ -d benchmarks/orbslam3/src/original ] ; then echo -e "\033[1;32mFound\033[0m" ; else echo -e "\033[1;31mNot found (make orbslam3)\033[0m" ; fi
@echo    "    repository: https://github.com/UZ-SLAMLab"
@echo    "    available targets are : orbslam3"
@echo    ""
```

and

```jsx
orbslam3:
@echo "================================================================================================================="
@echo    "  - ORBSLAM3 [Campos et al, ARXIV'20] "
@echo    "    Original repository: https://github.com/UZ-SLAMLab"
@echo    "    Used repository: https://github.com/mihaibujanca/ORB_SLAM3"
@echo "================================================================================================================="
@echo ""
@echo "Are you sure you want to download this use-case (y/n) ?" && ${GET_REPLY} && echo REPLY=$$REPLY && if [ ! "$$REPLY" == "y" ] ; then echo -e "\nExit."; false; else echo -e "\nDownload starts."; fi
mkdir -p benchmarks/orbslam3/src/original
rm benchmarks/orbslam3/src/original -rf
git clone https://github.com/mihaibujanca/ORB_SLAM3 benchmarks/orbslam3/src/original
@echo "cmake_minimum_required(VERSION 2.8)"   > benchmarks/$@/CMakeLists.txt
@echo "explore_implementations ( $@ src/* )"     >> benchmarks/$@/CMakeLists.txt
```

in appropriate area. }

`make orbslam3 orbslam2`

`cd slambench/benchmarks/orbslam3/src/original/Thirdparty/DBoW2`

`mkdir build && cd build`

`cmake .. -DCMAKE_BUILD_TYPE=Release`

`make -j2`

`cd slambench/benchmarks/orbslam3/src/original/Thirdparty/g2o`

`mkdir build && cd build`

`cmake .. -DCMAKE_BUILD_TYPE=Release`

`make -j2`

`cd slambench/benchmarks/orbslam3/src/original/Vocabulary`

`tar -xf ORBvoc.txt.tar.gz`

`cd slambench/benchmarks/orbslam2/src/original/Thirdparty/DBoW2`

`mkdir build && cd build`

`cmake .. -DCMAKE_BUILD_TYPE=Release`

`make -j2`

`cd slambench/benchmarks/orbslam2/src/original/Thirdparty/g2o`

`mkdir build && cd build`

`cmake .. -DCMAKE_BUILD_TYPE=Release`

`make -j2`

`cd slambench/benchmarks/orbslam2/src/original/Vocabulary`

`tar -xf ORBvoc.txt.tar.gz`

`cd slambench`

`make slambench APPS=orbslam3 orbslam2`


*This repositary contains updated SLAMBench by solving all the below errors.

# Errors 
 These are the errors obtained while building the original repo of slambench: [https://github.com/pamela-project/slambench](https://github.com/pamela-project/slambench)


1. unrcognized coand -mfpu=neon

sol: remove brisk from CmakeLists.txt file. Brisk is a dependency for OKVIS, here we are not using OKVIS


2. cannot find -lgfortran

sol: make gcc,g++,gfortan same version

first check the versions:

`gcc --version`

`g++ --version`

`gfortran --version`

If versions are different, then install:

`sudo apt-get update`

`apt-get install gcc-8 g++-8 gfortran-8`

`sudo update-alternatives --install /usr/bin/gfortran gfortran /usr/bin/gfortran-8 70`


3. checking build system type... ./config.guess: unable to guess system type

sol: update automake version to 1.16

remove the older version by:

`sudo apt-get autoremove automake`

update by:

`wget [http://ftp.gnu.org/gnu/automake/automake-1.16.tar.gz](http://ftp.gnu.org/gnu/automake/automake-1.16.tar.gz)`

`tar xvzf automake-1.16.tar.gz`

`cd automake-1.16`

`./configure`

`make -j3`

`sudo make install`

`sudo gedit /slambench/deps/repos/cvd/config.guess`

clear and paste content of

 [http://cvs.savannah.gnu.org/viewvc/*checkout*/config/config/config.guess](http://cvs.savannah.gnu.org/viewvc/*checkout*/config/config/config.guess)

`sudo gedit /slambench/deps/repos/cvd/config.sub`

clear and paste content of 

[http://cvs.savannah.gnu.org/viewvc/*checkout*/config/config/config.sub](http://cvs.savannah.gnu.org/viewvc/*checkout*/config/config/config.sub)


4. cd /home/nvidia/slambench/deps/repos ; git clone git://github.com/mariusmuja/flann.git flann
Cloning into 'flann'...
fatal: remote error:
The unauthenticated git protocol on port 9418 is no longer supported.
Please see [https://github.blog/2021-09-01-improving-git-protocol-security-github/](https://github.blog/2021-09-01-improving-git-protocol-security-github/) for more information.
framework/makefiles/flann.make:6: recipe for target '/home/nvidia/slambench/deps/repos/flann' failed

sol: inside /home/nvidia/slambench/framework/makefiles/flann.make

change git clone repo to [https://github.com/flann-lib/flann.git](https://github.com/flann-lib/flann.git)


5. checking build system type... ./config.guess: unable to guess system type

sol: 

`sudo gedit /slambench/deps/repos/gvars/config.guess `

clear and paste content of

 [https://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess](https://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess)


6. note: ...this statement, but the latter is misleadingly indented as if it were guarded by the ‘if’ if (* x) delete (* reinterpret_cast<T**>(x)); *x = NULL;

sol: Go to the file 

`gedit /home/nvidia/slambench/deps/repos/flann/src/cpp/flann/util/any.h`

and change line no:81 to

 if (* x){ delete (* reinterpret_cast<T**>(x));} *x = NULL;

 
 7. SLAMBench dataset build error  

[#######################################           ] 61258 / 80566Could not write frame data 

Failed to serialise frame 

Done. 

dataset-generator: /home/nvidia/slambench/framework/tools/dataset-tools/dataset-generator.cpp:197: int main(int, char**): Assertion `res' failed. 

framework/makefiles/dataset-utils.makefile:51: recipe for target 'datasets/EuRoCMAV/machine_hall/MH_01_easy/MH_01_easy.slam' failed 

make: *** [datasets/EuRoCMAV/machine_hall/MH_01_easy/MH_01_easy.slam] Aborted (core dumped) 

make: *** Deleting file 'datasets/EuRoCMAV/machine_hall/MH_01_easy/MH_01_easy.slam' 

Sol: This is due to lack of storage space in Jetson try adding SDCard or SSD externally

  
# slambench_agx
