cmake_minimum_required(VERSION 3.16)
project(SpectrumLiveStudio VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_PREFIX_PATH "$ENV{QTDIR}")

add_subdirectory(src/SpectrumLiveStudio)

