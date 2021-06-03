# Enviornment Setup

## install the build tools
> sudo apt-get install git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig


# Build Android Pie

## Initing the Repo
> repo init -u https://android.googlesource.com/platform/manifest -b android-9.0.0_r61

## Sync the repo
> repo sync -j8

## Setting up the enviornment
> source build/envsetup.sh 

## Choosing the device (6 is 64 bit)
> lunch aosp_x86_64-eng

## Build AOSP with 8 cores
> make -j8

## Build the ShellCommand application only
> make -j8 ShellCommand

## Run the emulator with current OS
> emulator


# Adding new application
1. Create folder vendor/keerthyapps/packages/ShellCommand (the foldername can be anything)
2. Copy the folders from Android Studio project -> java,res,AndroidManifest.xml
3. Create Android.mk file in the same directory (Described seperatly)
4. Open build/make/target/product/core.mk file and add module name ShellCommand to the last line of PRODUCT_PACKAGES (ShellCommand is the same name mentioned in Android.mk file)



# Android.mk file

An Android.mk file must begin by defining the LOCAL_PATH variable
> LOCAL_PATH:= $(call my-dir)

The next line declares the CLEAR_VARS variable, whose value the build system provides.
> include $(CLEAR_VARS)

Next, the LOCAL_MODULE variable stores the name of the module that you wish to build. Use this variable once per module in your application.
> LOCAL_PACKAGE_NAME := ShellCommand

The next line enumerates the source files within the directory java
> LOCAL_SRC_FILES := $(call all-java-files-under,java)

> LOCAL_SDK_VERSION := current

To disable progaurd
> LOCAL_PROGUARD_ENABLED := disabled

To sign the appliction with platform certificate
> LOCAL_CERTIFICATE := platform

To make the application system app. app will be placed in system/priv-apps if this is true
> LOCAL_PRIVILEGED_MODULE := true

As this is a system application, we need to add permission seperatly as a local module
> LOCAL_REQUIRED_MODULES := com.keerthy.shellcommand-def-perm.xml

To generage the APK file
> include $(BUILD_PACKAGE)

To process all the make files under this path
> include $(call all-makefiles-under,$(LOCAL_PATH))

To add the permission to etc folder to whitelist it
> include $(CLEAR_VARS)
> LOCAL_MODULE := com.keerthy.shellcommand-def-perm.xml
> LOCAL_MODULE_TAGS := optional
> LOCAL_MODULE_CLASS := ETC
> LOCAL_MODULE_PATH := $(TARGET_OUT_ETC)/default-permissions
 >LOCAL_SRC_FILES := default-permissions/com.keerthy.shellcommand.xml
> include $(BUILD_PREBUILT)


# Reference
- https://source.android.com/setup/build/initializing
- https://source.android.com/setup/build/downloading
- https://developer.android.com/ndk/guides/android_mk
- https://www.programmersought.com/article/1576992018/
