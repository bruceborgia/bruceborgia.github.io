---
layout: post
title: seetaface6&&OpenCV&&CMakeLists文件编写
tags: [目标检测]
data: 2023-05-30 18:01 +0800
---
# seetaface6&&OpenCV&&CMakeLists文件编写
#### seetaface6安装及编译

##### 1.下载

```
 git clone git@github.com:SeetaFace6Open/index.git --recursive
```

##### 2.框架

```
index
    |-- asserts               一些图片，无视掉     
    |-- docs                  各功能接口说明
    |-- example               包含了一个qt 人脸识别demo
    |-- FaceAntiSpoofingX6    静默活体模块
    |-- FaceBoxes             人脸检测模块
    |-- FaceRecognizer6       人脸识别模块
    |-- FaceTracker6          人脸跟踪
    |-- Landmarker            人脸特征点
    |-- OpenRoleZoo           *常用操作的集合*
    |-- PoseEstimator6        人脸朝向姿态估计
    |-- QualityAssessor3      图像质量评估（亮度评估、清晰度评估、完整度评估、清晰度评估（深度）、姿态评估、姿态评估（深度）、分辨率评估）
    |-- README.md
    |-- SeetaAgePredictor     年龄识别
    |-- SeetaAuthorize	      *模型解析工程*
    |-- SeetaEyeStateDetector 眼睛状态检测：睁眼，闭眼
    |-- SeetaGenderPredictor  性别识别
    |-- SeetaMaskDetector     口罩人脸检测识别
    |-- TenniS                *前向计算框架*


```

##### 3.编译  OpenRoleZoo

系统：Ubuntu18.04

OpenRoleZoo 为常用操作的集合，SeetaAuthorize 为模型解析工程，TenniS 为前向计算框架。需要重点说明的是，此次 TenniS 同时放出了 **GPU** 计算源码，可以编译出 **GPU** 版本进行使用。上述三个模块为基础模块，各个 SDK 的编译均依赖上述模块，因此需要优先编译出 OpenRoleZoo, SeetaAuthorize 和 TenniS，然后再进行其他 SDK 模块的编译。

```
cd OpenRoleZoo
cd craft
 ./build.linux.x64.sh 
```

<font color='#FF78'>编译报错：</font>

```
src/CMakeFiles/ORZ_STATIC.dir/build.make:316: recipe for target 'src/CMakeFiles/ORZ_STATIC.dir/orz/mem/pot.cpp.o' failed
make[2]: *** [src/CMakeFiles/ORZ_STATIC.dir/orz/mem/pot.cpp.o] Error 1
CMakeFiles/Makefile2:142: recipe for target 'src/CMakeFiles/ORZ_STATIC.dir/all' failed
make[1]: *** [src/CMakeFiles/ORZ_STATIC.dir/all] Error 2
Makefile:149: recipe for target 'all' failed

```

![image-20230525170957403](/home/yfzx/Blog/images/blog_images/image-20230525170957403.png)

解决方法：根据提示打开 "/seetaface6/index/OpenRoleZoo/include/orz/mem/pot.h",引用头文件functional

![image-20230525171546468](/home/yfzx/Blog/images/blog_images/image-20230525171546468.png)

随后再次编译，显示编译成功

##### 4.编译SeetaAuthorize

```
cd SeetaAuthorize
cd craft
 ./build.linux.x64.sh 
 chmod +777 build.linux.x64.sh
```

权限不足的话执行最后一条，再运行

##### 5.编译TenniS

```
cd TenniS/
cd craft
 ./build.linux.x64.sh 
 chmod +777 build.linux.x64.sh
```

##### 6.其他模块编译

与前三个模块一样，均运行craft文件夹中sh文件

```
cd craft
 ./build.linux.x64.sh 
```

# CMakeLists.txt文件调用seetaface6

##### cmake基本语法 

1. 在CMake中 使用'set'命令可以设置变量的值，cmake中，变量名是区分大小的，并且可以使用'${}'语法来引用变量的值。

   ```
   set(SeeINC_DIR /usr/local/seetaface6/include)
   #设置一个名为`SeeINC_DIR`的变量，并将其值设置为`/usr/local/seetaface6/include`
   set(SeeLINK_DIR /usr/local/seetaface6/lib64)
   
   ```

   #####  完整版

   ```
   #cmake最低版本号要求2.8
   cmake_minimum_required(VERSION 3.16)
   # 项目信息
   project(face_detect)
   
   # 指定语言版本
   set(CMAKE_CXX_STANDARD 14)
   
   
   
   # seetaface6
   set(Seetaface_INC_DIR /usr/local/seetaface6/include)
   set(Seetaface_LIB_DIR /usr/local/seetaface6/lib64)
   include_directories(${Seetaface_INC_DIR})
   link_directories(${Seetaface_LIB_DIR})
   
   #指定生成目标
   add_executable(face_detect main.cpp)
   
   target_link_libraries(face_detect -lSeetaAgePredictor600
           -lSeetaAuthorize
           -lSeetaEyeStateDetector200
           -lSeetaFaceAntiSpoofingX600
           -lSeetaFaceDetector600
           -lSeetaFaceLandmarker600
           -lSeetaFaceRecognizer610
           -lSeetaFaceTracking600
           -lSeetaGenderPredictor600
           -lSeetaMaskDetector200
           -lSeetaPoseEstimation600
           -lSeetaQualityAssessor300
           -ltennis
           -ltennis_haswell
           -ltennis_pentium
           -ltennis_sandy_bridge)
   
   
   ```

   target_link_libraraes是链接"/usr/local/seetaface6/lib64"中文件

   ![image-20230530175511077](/home/yfzx/.config/Typora/typora-user-images/image-20230530175511077.png)

   ![lib64文件](https://github.com/bruceborgia/images/raw/main/blog_images/12.png)

   前面的`-l`选项告诉链接器要链接一个库文件，后面的`SeetaAgePredictor600`是库文件的名称。

   #####　-l参数的使用

   ​		在使用`-l`选项指定库文件名称时，应注意库文件的命名规则和链接器的要求。确保库文件的名称和路径是正确		的，并且链接器能够找到这些库文件。

   ​		在链接器命令中，`-l`是一个选项，用于指定要链接的库文件的名称。它通常与库文件名一起使用。

   ​		当使用`-l`选项时，链接器会搜索具有指定名称的库文件，并将其链接到最终的可执行文件或共享库中。链接器会		根据操作系统和配置的不同，在默认的库搜索路径中查找该库文件。

   ​		例如，假设你要链接名为`mylib`的库文件，你可以使用`-l`选项将其指定为`-lmylib`。链接器会搜索名为`libmylib.so`	（在Linux/Unix系统上）或`mylib.lib`（在Windows系统上）的库文件，并将其链接到你的程序中。

   ​		在CMake中，当使用`target_link_libraries`命令时，你可以直接使用`-l`选项来链接库文件，而无需手动指定完整的		库文件名称。

   ​		例如，`target_link_libraries(mytarget -lmylib)`将会链接名为`mylib`的库文件到名为`mytarget`的目标中。

   ​		请注意，使用`-l`选项时，不需要指定库文件的前缀`lib`和后缀`.so`或`.lib`。链接器会根据操作系统和约定自动搜索		正确的库文件。

   # CMakeLists.txt文件调用OpenCV
   
   ```
   find_package(OpenCV)
   include_directories(${OpenCV_INCLUDE_DIRS})
   #指定生成目标
   add_executable(face_detect main.cpp)
   target_link_libraries(face_detect ${OpenCV_LIBRARIES})
   ```

   对于CMake中的`find_package(OpenCV)`命令，它会自动设置一些相关的变量，包括`OpenCV_INCLUDE_DIRS`，用于指定OpenCV的包含目录。

   还可以查看其他cmake自动设置的变量：
   
   ```
   message(STATUS "OpenCV include dirs: ${OpenCV_INCLUDE_DIRS}")
   message(STATUS "OpenCV library dirs: ${OpenCV_LIBRARY_DIRS}")
   message(STATUS "OpenCV libraries: ${OpenCV_LIBRARIES}")
   message(STATUS "OpenCV version: ${OpenCV_VERSION}")
   message(STATUS "OpenCV found: ${OpenCV_FOUND}")
   ```

   在终端中使用命令：
   
   ```
   cmake -DCMAKE_MESSAGE_LOG_LEVEL=STATUS ..
   ```

   即可查看，或者在cmake日志中查看

   1. `OpenCV_INCLUDE_DIRS`：OpenCV的包含目录列表。

   2. `OpenCV_LIBRARY_DIRS`：OpenCV的链接目录列表。

   3. `OpenCV_LIBRARIES`：OpenCV的库文件列表。

   4. `OpenCV_VERSION`：OpenCV的版本号。

   5. `OpenCV_FOUND`：表示是否找到了OpenCV。

      

   ##### 其他参数
   
   ```
   find_package(OpenCV REQUIRED)
   ```

   如果你不使用`REQUIRED`参数，即`find_package(OpenCV)`，如果找不到OpenCV，CMake会显示警告信息，但不会阻止构建过程。这在某些情况下可能是可行的，例如，如果你的项目有备用方案来处理OpenCV缺失的情况。	

   #####　完整版
   
   ```
   #cmake最低版本号要求2.8
   cmake_minimum_required(VERSION 3.16)
   # 项目信息
   project(face_detect)
   
   # 指定语言版本
   set(CMAKE_CXX_STANDARD 14)
   
   #opencv
   find_package(OpenCV)
   include_directories(${OpenCV_INCLUDE_DIRS})
   
   # seetaface6
   set(Seetaface_INC_DIR /usr/local/seetaface6/include)
   set(Seetaface_LIB_DIR /usr/local/seetaface6/lib64)
   include_directories(${Seetaface_INC_DIR})
   link_directories(${Seetaface_LIB_DIR})
   
   #指定生成目标
   add_executable(face_detect main.cpp)
   
   target_link_libraries(face_detect ${OpenCV_LIBRARIES})
   target_link_libraries(face_detect -lSeetaAgePredictor600
           -lSeetaAuthorize
           -lSeetaEyeStateDetector200
           -lSeetaFaceAntiSpoofingX600
           -lSeetaFaceDetector600
           -lSeetaFaceLandmarker600
           -lSeetaFaceRecognizer610
           -lSeetaFaceTracking600
           -lSeetaGenderPredictor600
           -lSeetaMaskDetector200
           -lSeetaPoseEstimation600
           -lSeetaQualityAssessor300
           -ltennis
           -ltennis_haswell
           -ltennis_pentium
           -ltennis_sandy_bridge)
   
   
   ```
   
   
