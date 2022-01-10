---
title: "Compile Android Spice(aSpice)"
date: "2013-09-02"
categories: 
  - "cloud"
---

Ref: [http://comments.gmane.org/gmane.comp.emulators.spice.devel/13944](http://comments.gmane.org/gmane.comp.emulators.spice.devel/13944 "http://comments.gmane.org/gmane.comp.emulators.spice.devel/13944") [https://github.com/iiordanov/remote-desktop-clients/blob/master/BUILDING](https://github.com/iiordanov/remote-desktop-clients/blob/master/BUILDING)

Before following the BUILDING instruction in the repo, you need download [android-ndk-r9d](http://dl.google.com/android/ndk/android-ndk-r9d-linux-x86_64.tar.bz2), or else you will get lots of error.

I was using Eclipse for android neon, android-sdk-25.

cebero is no longer needed. **update 2016**

编译环境
ubuntu-14.04，按理12.04、16.04也可以

1. 下载所需包并解压
android-ndk-r9d-linux-x86\_64.tar.bz2
gstreamer-1.0-android-arm-release-1.4.5.tar.bz2

链接：http://pan.baidu.com/s/1c1AWPmo 密码：t4ku

demo@ubuntu:~$ tar xf Downloads/android-ndk-r9d-linux-x86\_64.tar.bz2 -C /home/demo/
demo@ubuntu:~$ sudo tar xf Downloads/gstreamer-1.0-android-arm-release-1.4.5.tar.bz2 -C /opt/gstreamer

2. 修改bVNC为aSPICE
在文件~/remote-desktop-clients/eclipse\_projects/bVNC/AndroidManifest.xml中，
将android:label="bVNC" and android:name="bVNC"修改为android:label="aSPICE" and android:name="aSPICE" respectively.

然后在这个bVNC目录中执行命令：
demo@ubuntu:bVNC$ find jni/src -name \\\*.h -o -name \\\*.c -exec sed -i 's/aSPICE/bVNC/' {} \\;

3. 编译
demo@ubuntu:bVNC$ /home/demo/android-ndk-r9d/ndk-build GSTREAMER\_SDK\_ROOT\_ANDROID=/opt/gstreamer -j 4

1\. Install/update sqlitegen plugin v0.1.19 into Eclipse from: https://github.com/iiordanov/sqlitegen-secure/releases/download/v0.1.19/sqlitegen\_eclipse\_site\_0.1.19.jar

NOTE: Using an older version of the plugin may not work because of the switch to the sqlcipher release of sqlite.

2\. Clone the FreeRDP project in the parent directory of where you cloned bVNC project: git clone git://github.com/FreeRDP/FreeRDP.git

Check out a commit known to work with bVNC: git checkout stable-1.1

You should now have bVNC and FreeRDP side by side in the same directory.

Add bVNC, ZoomerWithKeys and PubkeyGenerator from the bVNC/eclipse\_projects/ directory to Eclipse as existing Android projects. Add FreeRDPCore from the FreeRDP/client/Android/ directory to Eclipse as an Existing Android Project.

3\. Ensure ZoomerWithKeys, PubkeyGenerator, and FreeRDPCore projects are called exactly as written here by right-clicking on each of them, then selecting Refactor->Rename

3\. Add the ZoomerWithKeys, PubkeyGenerator, and FreeRDPCore projects as Library References to bVNC (right-click on the bVNC project and select Properties -> Android -> Library). If they are not, add them.

4\. Ensure that ZoomerWithKeys, PubkeyGenerator, and FreeRDPCore are specified as "Required projects on the build path" (right- click on the bVNC project, and select Properties -> Java Build Path -> Projects). If they are not, add them.

5\. Ensure that ZoomerWithKeys, PubkeyGenerator, and FreeRDPCore are referenced in Properties -> Project References (put checkmarks on projects).

6\. To build and successfully run aSPICE, you need to: - Unpack gstreamer-sdk 1.4.5 into /opt/gstreamer. I obtained my copy from: https://gstreamer.freedesktop.org/data/pkg/android/1.4.5/gstreamer-1.0-android-arm-release-1.4.5.tar.bz2 Newer versions either crash or have GNUtls errors that need work.

\- Edit AndroidManifest.xml and change android:label="bVNC" and android:name="bVNC" to android:label="aSPICE" and android:name="aSPICE" respectively.

\- cd bVNC/eclipse\_projects/bVNC

\- Replace all instances of aSPICE with bVNC in native code (.c and .h files). This requirement should go away in the future. You can use the following command: find jni/src -name \\\*.h -o -name \\\*.c -exec sed -i 's/aSPICE/bVNC/' {} \\;

\- ndk-build GSTREAMER\_SDK\_ROOT\_ANDROID=/opt/gstreamer -j 4

\- Unfortunately, currently ndk-build destroys other pre-existing libraries in eclipse\_projects/bVNC/libs, so after successfully building libspice and libgstreamer, one has to git checkout the files which were deleted.

\- Force one boolean variable to be true. You can use the following command: find ./ -name \\\*.java -exec sed -i 's/isSpice = .\*;/isSpice = true;/' {} \\;

\- You may have to select the "Android private libraries" entry in Project Properties->Java Build Path->Order and Export if you have it.

\- Enable requesting the RECORD\_AUDIO permission in AndroidManifest.xml with the following sed command: sed -i 's//<\\/uses-permission>/g' AndroidManifest.xml

Please inform me if (6) needs to be expanded (i.e. if you needed to do anything else in addition to the above.
