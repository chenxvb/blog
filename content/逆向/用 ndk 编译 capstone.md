---
title: "用 ndk 编译 capstone"
draft: false
tags:
  - reverse
  - andorid
  - ndk
---

## 如何编译 capstone 的 ndk android 跨平台库

感觉 NDK 已经半截入土了，很多软件的新版本都很少用到 ndk，谷歌感觉在这方面也摆烂了。印象中貌似现在的 android native 开发也开始用 cmake 替代了，反正后面肯定是要寄了。
但我现在有个项目就要在 ndk 用到 capstone ，查了下发现之前 capstone 都是有支持的，但现在的新文档都没有提到 ndk，为了保险我这边还是选择了老版本的 capstone (master 分支)
在该分支的文件里中有明确写道：

```
(5) Cross-compile for Android

  To cross-compile for Android (smartphone/tablet), Android NDK is required.
  NOTE: Only ARM and ARM64 are currently supported.

	$ NDK=/android/android-ndk-r10e ./make.sh cross-android arm
  or
	$ NDK=/android/android-ndk-r10e ./make.sh cross-android arm64

  Resulted files libcapstone.so, libcapstone.a & tests/test* can then
  be used on Android devices.
```
提前下好 ndk r20 [google download](https://github.com/android/ndk/wiki/Unsupported-Downloads)
确保 python 环境没有问题（python 为 python3 之类的）
然后再 capstone 目录下编译 `NDK=/path/to/ndk/android-ndk-r20b ./make.sh cross-android arm64`
编译完成后就可以在 `Android.mk` 下面添加 `LOCAL_LDFLAGS := -L/path/to/you/build/capstone -lcapstone`
示例：

```
LOCAL_PATH := $(call my-dir)  

include $(CLEAR_VARS) 
# LOCAL_C_INCLUDES := $(LOCAL_PATH)/capstone

LOCAL_MODULE := inject
LOCAL_SRC_FILES := injector.c shellcode.s myasm.c payload.s

LOCAL_LDLIBS += -L$(SYSROOT)/usr/lib -llog  

LOCAL_LDFLAGS := -L/home/vior/code/capstone -lcapstone

include $(BUILD_EXECUTABLE)
```
然后设置好 ndk 环境变量，我使用的是 ndk-27c
```
export NDK_HOME=/path/to/android-ndk-r27c
export PATH=$PATH:$NDK_HOME
```
后面再 `ndk-build` 即可