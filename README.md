# SPECTRUM Live Studio
![spectrumlive](https://github.com/user-attachments/assets/fdb88ec5-04c2-470e-99d1-05d35c2bcb47)


**Visit [our official web site](http://spectrumlive.xyz) for more information and [Latest updates on SPECTRUM Live Studio](http://spectrumlive.xyz/pcapp/)**.

## About SPECTRUM Live Studio
SPECTRUM Live studio PC version helps beginners stream like professionals. It is a desktop application for live broadcasting.
With easy operation, anyone can easily make broadcasts and send it stably to various platforms.

SPECTRUM Live Studio used the OBS engine as the core module. We would thank all the developers with their wonderful work of OBS project.

## BUILD
#### Before build, please prepare install XCode and QT 6.3.1 version first and set the enviroment variables as:
```
QTDIR: QT install directory/6.3.1/macos
```
```
export QTIDR=/Users/username/Qt/6.5.3/macos
```
#### to build deployment follow below command
```
$ cmake -B cmake-build-release -DCMAKE_BUILD_TYPE=Release -DDEPLOYMENT_BUILD=ON
$ cmake --build cmake-build-release
```

#### to build and debugging follow below command
```
$ cmake -B cmake-build-debug -DCMAKE_BUILD_TYPE=Debug
$ cmake --build cmake-build-debug
```

## Build on Clion
#### we need to set the QTDIR variable in the project.
1. Go to Run | Edit Configurations. ...
2. In the Run/Debug Configurations dialog that opens, select a configuration where you want to pass the arguments and add the environment variables.
3. Type the arguments in the Program arguments field. ...
4. In the Environment variables field, type the variable name and value: QTIDR=/Users/username/Qt/6.5.3/macos

