Subsurface Qt for Android
--------------------------

One of the reasons behind choosing Qt as UI interface for Subsurface has been
its cross platform support. Qt supports android as well. Cross compilation of
subsurface for android has been done by Anton Lundin and can be seen [here]
(http://github.com/glance-/subsurface-android).

One of the shortcomings of this version was lack of USB support. One of the
most common applications of subsurface is download of dives from divecomputers.
This is done via libdivecomputer library. But due to added complications of USB
permission on android, this is not possible in a straightforward manner.

The patches included provide a way for getting USB permissions and provision of
dive download from divecomputers for subsurface-qt-android.

For getting USB permissions, the only way is usage of jave Android USB APIs.
Hence, for getting permissions from the native side, we need to use Java Native
Interface. This is done in the attached patches. On getting the permissions, the
UsbDevice is opened and its file descriptor is obtained. This is passed to the
native side and used for communication with the divecomputers. Libdivecomputer
has been appropriately modified for this purpose.

###Usage

1. **Permissions**

Searching the android documentations, I found an interesting way which would
ease up Usb permission gathering on android. Android has provision of listing
VIDs and PIDs of various devices for which your application should open
automatically. Applications opened this way would already have permission for
usage of the device without any extra hassle. The only thing needed to be done
is providing device_filter.xml listing all needed VIDs and PIDs. As a start,
only FTDI devices are supported. Hence, VID and PID (both standard and custom)
of various FTDI divecomputers are listed there. Android USB APIs can be viewed
[here]().

2. **Qt Android Building**

Building Qt for android is peculiar. Qt has a set of prespecified libraries
present which it copies to the Android project folder. These Qt libraries can
then be used by Qt applications on android. Hence, there is a template android
application present. For adding things like java classes, customised
AndroidManifest and other stuffs such as image files and such, one needs to
follow the following steps. Relevant documents can be viewed
[here](http://qt-project.org/doc/qt-5/deployment-android.html#qmake-variables).

1. Copy the template to a particular folder, say qt_template.

2. Reference this folder as variable ANDROID_PACKAGE_SOURCE_DIR in your Qt
   project build file (.pro or .pri).
   ```
   ANDROID_PACKAGE_SOURCE_DIR = qt_template.
   ```

3. Build the project normally and deploy on android using androiddeployqt.

androiddeployqt would first copy its own template to the output directory
(referenced by --output on commandline). Then it would copy the qt_template
folder contents to the output directory. This would overwrite any existing files
present in the output folder.

This property can be used, as mentioned, for addition of various customisations
to the qt application. We have used this to add USB permission xml file and
resource files (like icons and strings.xml).

### Patches

The included patches provide the following changes to subsurface-android master.

1. Patches-for-libusb-and-libftdi-passing-fd.patch

Download, patch and build libftdi and additional patches for libusb for usb
file descriptor passing.


### Subsurface patches.

On the top of these, there are changes for subsurface upstream for working with
android qt. They are mentioed below.

1. Open-subsurface-on-attaching-divecomputer.patch

Using the VID and PID of device_filter.xml, open subsurface on attaching any
matching divecomputer. The permissions for usage of USB are also granted to the
application.

2. Extract-usb-file-descriptor-of-ftdi-on-Android.patch

Usb extraction from the native side is done here. This uses Qt JNI
implementation to deal with Java Android USB APIs.

3. On-android-use-file-descriptor-instead-of-name.patch

For building on android, pass the file descriptor casted as (const void \*).

### Known bugs

One of the known bugs is due to the JNI permissions approach. We have a
device_filter.xml file having the list of supported VIDs and PIDs. Any USB
device matching these is attached, would cause a permission dialog to appear,
accepting which the Subsurface application will open. Rejecting the dialog and
opening the application on its own would not give the permission to the
application and cause a Permission Error. For getting the permission one can try
detaching and reattaching. This behaviour is far from ideal.

Due to deadlocks occurring in serial_ftdi on libdivecomputer, the application
sometimes freezes.

### Upstream status

One of the patches sent to the subsurface has been merged to the master. It
is included here in merged folder. The others are not yet sent and require
some trimming and polishing.

