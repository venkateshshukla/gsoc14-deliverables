Subsurface android jni
-----------------------

If subsurface as a library is to be used on android without its qt components,
   there should be some changes made to the subsurface as is. These changes are
   implemented in the patches attached. Some explanation is also provided below.

   The complete approach was to remove qt related files and stub out unneeded
   functions. After this, we get a subsurface_jni.a library using which android
   subsurface downloader can be made.

###Build Instructions

The patches can be applied using the commands
> git am patch_name.patch

In order to build subsurface_jni.a use the following commands.

```
$QT5_ANDROID_BIN/qmake ../../subsurface QT_CONFIG=+pkg-config CONFIG+=android_jni
make
make install INSTALL_ROOT=${INSTALL_DIR}
```
Where QT5_ANDROID_BIN is bin dir of qt for android and INSTAL_DIR is where the
lib is expected to be installed.

###Patches

1. Build-subsurface_jni-for-building-on-android.patch

For building on android subsurface downloader, we would need only the core
libraries of subsurface. All the unnecessary stuff is hence removed for
android_jni build.


2. Pass-void-devparam-rather-than-char-name.patch

This would enable passing of both int file descriptor on android as well as path
to tty on posix and win32.

3. No-pw_gecos-on-android.patch

replace with pw_name

4. added-divecomputer_android-for-subsurface_jni.patch

When compiling for subsurface_jni, use divecomputer_android.[ch] files. All the
relevant functions of divecomputer.c are implemented here.

5. getline-definition-for-android.patch

getline definition is not present in android bionic libraries. linux.c requires
this definition. Hence, add the definitions for building android_jni.
The getline file is taken from http://goo.gl/hm5ldd.

6. gettext-c-for-android_jni.patch

For a beginning, translations are not incorporated on android. gettext.c
implements trGettext for non Qt android_jni build.

7. androidhelper.c-implementing-remaining-functions.patch

The functions still remaining and giving undefined references are implemented here.

### Upstream status

These patches are not finalised and need some more work. After finalisatio, they
will be sent to subsurface mailing list for merge.


