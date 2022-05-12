# SLAMBench_jetson xavier agx
SLAMBench(updated repo) is used here for benchmarking ORBSLAM2, ORBSLAM3 on NVIDIA Jetson Xavier agx

Original repo: https://github.com/pamela-project/slambench.git

Paper: [Robust SLAM Systems: Are We There Yet?](https://arxiv.org/abs/2109.13160) \
https://robustslam.github.io/evaluation

## What is SLAMBench? ##

SLAMBench is a SLAM performance benchmark that combines a framework for quantifying quality-of-result with instrumentation of accuracy, execution time, memory usage and energy consumption. It also include a graphical interface to visualize these information.

SLAMBench offers a platform for a broad spectrum of future research in jointly exploring the design space of algorithmic and implementation-level optimisations. It targets desktop, laptop, mobile and embedded platforms. Some of the benchmarks (in particular KFusion) were tested on Ubuntu, OS X and Android (more information about android here [https://github.com/bbodin/slambench-android](https://github.com/bbodin/slambench-android)).

### Hardware Specification for Nvidia Jetson AGX used here:

GPU:	512-core Volta GPU with Tensor Cores

CPU:	8-core ARM v8.2 64-bit CPU, 8MB L2 + 4MB L3

Memory:	32GB 256-Bit LPDDR4x | 137GB/s

Storage:	32GB eMMC 5.1 

OS: UBUNTU 18.04



**IMPORTANT: If you use any of those algorithms in scientific publications, you should refer to the respective publications.**

In addition, if you use SLAMBench in scientific publications, we would appreciate citations to the following papers:
```

@inproceedings{bujanca2021robust,  
  author={Bujanca, Mihai and Shi, Xuesong and Spear, Matthew and Zhao, Pengpeng and Lennox, Barry and Luj{\'a}n, Mikel},
  booktitle={2021 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS)},
  title={Robust SLAM Systems: Are We There Yet?},
  year={2021},
  doi={10.1109/IROS51168.2021.9636814}
}

@inproceedings{bujanca2019slambench,
  title={SLAMBench 3.0: Systematic automated reproducible evaluation of SLAM systems for robot vision challenges and scene understanding},
  author={Bujanca, Mihai and Gafton, Paul and Saeedi, Sajad and Nisbet, Andy and Bodin, Bruno and O'Boyle, Michael FP and Davison, {Andrew J} and Kelly, {Paul H.J.} and Riley, Graham and Lennox, Barry and Luj{\'a}n, Mikel and Furber, Steven},
  booktitle={2019 International Conference on Robotics and Automation (ICRA)},
  pages={6351--6358},
  year={2019},
  organization={IEEE}
}

@inproceedings{Bodin2018,
author    = "Bruno Bodin and Harry Wagstaff and Sajad Saeedi and Luigi Nardi and Emanuele Vespa and Mayer, {John H} and Andy Nisbet and Mikel Luj{\'a}n and Steve Furber and Davison, {Andrew J} and Kelly, {Paul H.J.} and Michael O'Boyle",
title     = "SLAMBench2: Multi-Objective Head-to-Head Benchmarking for Visual SLAM",
booktitle = "{IEEE Intl. Conf. on Robotics and Automation (ICRA)}",
year = {2018},
month = {May}
}

@inproceedings{Nardi2015,
  title={Introducing SLAMBench, a performance and accuracy benchmarking methodology for SLAM},
  author={Nardi, Luigi and Bodin, Bruno and Zia, M Zeeshan and Mawer, John and Nisbet, Andy and Kelly, Paul HJ and Davison, Andrew J and Luj{\'a}n, Mikel and O'Boyle, Michael FP and Riley, Graham and others},
  booktitle={2015 IEEE international conference on robotics and automation (ICRA)},
  pages={5783--5790},
  year={2015},
  organization={IEEE}
}
```

### Available list of algorithms

Use the following command to list all available algorithms:

```
make usecases
```

# Steps for building SLAMBench on Jetson Xavier AGX

ALGORITHMS BENCHMARKED: ORBSLAM2, ORBSLAM3
### Dependency installation

#### Required by SLAMBench framework
* CMake 2.8.11 or higher is required.
* Make
* GCC C/C++
* Boost (Optional)
* GLUT (Optional)

#### Required by benchmarks and datasets
* Git
* Mercurial
* wget
* unzip
* lapack
* blas
* findutils
* cvs
* glog
* gflags
* p7zip

Installation of library Dependencies

`sudo apt-get -y install libvtk6.3 libvtk6-dev unzip libflann-dev wget
mercurial git gcc g++ cmake python-numpy freeglut3 freeglut3-dev
libglew-dev libglu1-mesa libglu1-mesa-dev libgl1-mesa-glx
libgl1-mesa-dev libxmu-dev libxi-dev libboost-all-dev cvs
libgoogle-glog-dev libatlas-base-dev gfortran gtk2.0 libgtk2.0-dev
libyaml-dev build-essential libyaml-cpp-dev`

#### Special requirements for CUDA

To run the CUDA implementation of some of the algorithms, you will need extra dependencies.

`sudo apt-get -y install nvidia-cuda-toolkit clinfo`


`git clone https://github.com/pamela-project/slambench `

`cd slambench`

`make deps` //installing dependencies (Details on dependencies is found inside slambench/framework/makefiles/README.md file)

`make slambench`   //slambench framework compilation

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

`make orbslam3 orbslam2`  //download use-cases

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

`make slambench APPS=orbslam3,orbslam2`//compiling specific use-cases


SLAMBench currently supports the following algorithms:

* ORB-SLAM3 [Campos et al, ARXIV'20]: C++ as distributed by https://github.com/UZ-SLAMLab
* ReFusion [Palazollo et al. IROS'19]: CUDA as distributed by https://github.com/PRBonn
* OpenVINS [Geneva et al. IROS'19]: C++ as distributed by https://github.com/rpng/
* Supereight [Vespa et al. RA-L'18]: C++, OpenMP as distributed by https://github.com/emanuelev
* BundleFusion [Dai et al. ACM TOG'17]: CUDA as distributed by https://github.com/niessner
* SemanticFusion [McCormac et al. ICRA'17]: CUDA as distributed by https://github.com/seaun163
* ORB-SLAM2 [Mur-Artal et al, TOR'15 and TOR'17]: C++ as distributed by https://github.com/raulmur
* DSO [Engel et al. Arxiv'16]: C++ as distributed by https://github.com/JakobEngel
* ElasticFusion [Whelan et al, IJRR'16]: CUDA as distributed by https://github.com/mp3guy
* InfiniTAMv2 [Kahler et al, ISMAR'15]: C++, OpenMP and CUDA versions as distributed by https://github.com/victorprad/
* KinectFusion [Newcombe et al. ISMAR'11]: C++, OpenMP, OpenCL and CUDA inspired by https://github.com/GerhardR
* LSDSLAM [Engel et al, ECCV'14]: C++, and threaded as distributed by https://github.com/tum-vision/ and modified by https://github.com/mp3guy
* MonoSLAM [Davison et al, TPAMI'07]: Original version as distributed by https://github.com/hanmekim/
* OKVIS [Leutenegger et al, IJRR'15]: Original version as distributed by https://github.com/ethz-asl
* PTAM [Klein et al, ISMAR'07 and ECCV'08]: Original version as distributed by https://github.com/Oxford-PTAM/
* SVO [Forster et al, ICRA'14]: Original version as distributed by https://github.com/uzh-rpg/rpg_svo/ (a more recent version available at http://rpg.ifi.uzh.ch/svo2.html)

### Dependency installation

#### Required by SLAMBench framework
* CMake 2.8.11 or higher is required.
* Make
* GCC C/C++
* Boost (Optional)
* GLUT (Optional)

#### Required by benchmarks and datasets
* Git
* Mercurial
* wget
* unzip
* lapack
* blas
* findutils
* cvs
* glog
* gflags
* p7zip




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
