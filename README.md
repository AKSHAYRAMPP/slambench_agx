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

#### Installation on Jetson AGX

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

#### Downloading Dataset
To test a SLAM algorithm you can use a Live camera, or a dataset. 
SLAMBench provides tools to automatically download some of the most popular datasets. The file format (*.slam) will then include all the most important information about the dataset, those are **Camera calibration setting**, **initial position of the sensors**, and the **ground truth**.

As an example to download and generate EuRoC MAV dataset(Machine Hall 1), you can run the following :

`make datasets/EuRoCMAV/machine_hall/MH_01_easy/MH_01_easy.slam`

Complete list of the datasets available is provided by the command 

`make datasets`

#### Algorithms
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

#### Dataset

SLAMBench currently supports the following datasets:

* OpenLORIS [Shi et al, ICRA'20]: Lifelong SLAM dataset
* Bonn Dynamic [Palazollo et al. IROS'19]: Dynamic scene dataset
* UZH-FPV [Delmerico et al. ICRA'19]: Drone racing dataset
* ETH Illumination [Park et al, ICRA'17]: Illumination changes dataset
* VolumeDeform [Innmann et al, ECCV'16]: Non-rigid reconstruction
* EuRoC MAV [Burri et al, IJRR'16]: Micro Aerial Vehicle dataset
* ICL-NUIM [Handa et al, ICRA'14]: Synthetic dataset
* TUM RGB-D [Sturm et al, IROS'12]: A standard SLAM benchmark


## How to add a new benchmark in SLAMBench?

The main reason to provide a new version of SLAMBench is not only because of the introduction of new benchmarks but also because we provide now 
a clear and specific API for SLAM algorithms to be implemented in order to add a new algorithm.

```
bool sb_new_slam_configuration(SLAMBenchLibraryHelper * slam_settings);
bool sb_init_slam_system(SLAMBenchLibraryHelper * slam_settings);
bool sb_update_frame(SLAMBenchLibraryHelper * slam_settings, slambench::io::SLAMFrame * type);
bool sb_process_once(SLAMBenchLibraryHelper * slam_settings);
bool sb_relocalize(SLAMBenchLibraryHelper * slam_settings);
bool sb_update_outputs(SLAMBenchLibraryHelper *lib, const slambench::TimeStamp *latest_output);
bool sb_clean_slam_system();
bool sb_update_outputs(SLAMBenchUI *);
```

**If each of those functions are correctly implemented for a specific implementation of a specific algorithm, then this algorithm is compatible with SLAMBench and can be evaluated as well.**

In this section we will present those functions one by one.

### bool sb\_new\_slam\_configuration(SLAMBenchLibraryHelper * slam\_settings)

This function is called first, and only once, SLAM systems is expected to provide its parameters.

Example :

```cpp
bool sb_new_slam_configuration(SLAMBenchLibraryHelper * slam_settings)  {
	slam_settings->addParameter(TypedParameter<float>("c", "confidence",    "Confidence",   &confidence,    &default_confidence));
	slam_settings->addParameter(TypedParameter<float>("d", "depth",         "Depth",        &depth,         &default_depth));
	slam_settings->addParameter(TypedParameter<int>  ("td", "textureDim",   "textureDim",   &textureDim,    &default_textureDim));
	return true;
}
```

should always return `true` or an exception will be raised.


### bool sb\_init\_slam\_system(SLAMBenchLibraryHelper * slam\_settings)

This function is called second, and only once, SLAM systems is expected to allocate memory, retrieve sensor informations.


To retrieve sensor there is `SensorFinder`:
```cpp
slambench::io::CameraSensorFinder sensor_finder;
auto rgb_sensor = sensor_finder.FindOne(slam_settings->get_sensors(), {{"camera_type", "rgb"}});
```

SLAM systems are also expected to define there output, there is one mandatory output, the pose:

```cpp
pose_output = new slambench::outputs::Output("Pose", slambench::values::VT_POSE, true);
slam_settings->GetOutputManager().RegisterOutput(pose_output);
```
should always return `true` or an exception will be raised.

### bool sb_update_frame (SLAMBenchLibraryHelper *slam_settings, slambench::io::SLAMFrame *frame)

Algorithms receive frames ordered by timestamp.
When `sb_update_frame` returns `false`, `sb_update_frame` will be directly called again with the next frame, if it returns `true`, `sb_process_once` will be called once.

### bool sb_process_once (SLAMBenchLibraryHelper *slam_settings)

Should always return `true` or an exception will be raised.

### bool sb_relocalize (SLAMBenchLibraryHelper *slam_settings)

This is newly introduced to support lifelong SLAM evaluation. It will be called when the input sequence has been switched to the next one. The implementation is expected to explicitly trigger tracking lost and invoke the algorithm's re-localization procedure (if there be). It should return whether the relocalization is sucessful from the algorithm's perspective.

For backward compatibility, this function is allowed to be unimplemented in a benchmark. In such cases, the ```sb_process_once``` function will be called in a re-localization situation.

### bool sb_clean_slam_system()

This function is called last, and only once, SLAM systems is expected to clean everything (free memory).

```cpp
bool sb_clean_slam_system() {
	delete eFusion;
	delete inputRGB;
	delete inputDepth;
	return true;
}
```

should always return `true` or an exception will be raised.

### bool sb_update_outputs(SLAMBenchLibraryHelper *slam_settings, const slambench::TimeStamp *timestamp) 

The algorithm will return visible outputs (Pose, Point cloud, Frames) as defined by the `sb_init_slam_system` function.

Example :

```cpp
bool sb_update_outputs(SLAMBenchLibraryHelper *lib, const slambench::TimeStamp *ts_p) {
slambench::TimeStamp ts = *ts_p;

if(pose_output->IsActive()) {
	// Get the current pose as an eigen matrix
	Eigen::Matrix4f mat = eFusion->getCurrPose();

	std::lock_guard<FastLock> lock (lib->GetOutputManager().GetLock());
	pose_output->AddPoint(ts, new slambench::values::PoseValue(mat));
}
```

should always return `true` or an exception will be raised.
