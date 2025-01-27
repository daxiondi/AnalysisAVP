﻿cmake_minimum_required(VERSION 3.15)

# 获取当前文件夹名
STRING(REGEX REPLACE ".*/(.*)" "\\1" CURRENT_FOLDER ${CMAKE_CURRENT_SOURCE_DIR})

project(${CURRENT_FOLDER}_example)

# 包含路径
include_directories(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/../../3rdparty/libde265/include/)

if(WIN32)
# 分别设置了Debug版本和Release版本可执行文件的输出目录(经测试,windows环境有效)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../bin/windows/debug)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../bin/windows/release)
if(CMAKE_CL_64)
#win64
link_directories(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/../../3rdparty/libde265/lib/windows/x64)
else()
#win32
link_directories(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/../../3rdparty/libde265/lib/windows/win32)
endif(CMAKE_CL_64)
endif(WIN32)

if(ANDROID)
# 分别设置了Debug版本和Release版本可执行文件的输出目录(经测试,windows环境有效)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../bin/android/debug)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../bin/android/release)
link_directories(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/../../3rdparty/libde265/lib/android/${ANDROID_ABI})
endif(ANDROID)

# 添加源码文件到项目
FILE(GLOB header ./*.h)
aux_source_directory(. SRCS)
add_executable(${PROJECT_NAME} ${header} ${SRCS})

#windows
if(WIN32)
#static
add_definitions(-DLIBDE265_STATIC_BUILD)
target_link_libraries(${PROJECT_NAME} libde265-md.lib)
#shared
#target_link_libraries(${PROJECT_NAME} libde265-md.dll.lib)
endif(WIN32)

#android
if(ANDROID)
target_link_libraries(${PROJECT_NAME} libde265)
endif(ANDORID)

#windows
if(WIN32)
if(CMAKE_CL_64)
set_property(TARGET ${PROJECT_NAME} PROPERTY DEBUG_POSTFIX _x64_d)
set_property(TARGET ${PROJECT_NAME} PROPERTY RELEASE_POSTFIX _x64_r)
else(WIN32)
set_property(TARGET ${PROJECT_NAME} PROPERTY DEBUG_POSTFIX _win32_d)
set_property(TARGET ${PROJECT_NAME} PROPERTY RELEASE_POSTFIX _win32_r)
endif(CMAKE_CL_64)
endif(WIN32)

if(ANDROID)
set_property(TARGET ${PROJECT_NAME} PROPERTY DEBUG_POSTFIX _${ANDROID_ABI}_d)
set_property(TARGET ${PROJECT_NAME} PROPERTY RELEASE_POSTFIX _${ANDROID_ABI}_r)
endif(ANDORID)
