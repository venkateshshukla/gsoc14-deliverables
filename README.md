GSOC '14 : Subsurface android downloader
----------------------------------------
```
Project Duration : 19 May 2014 to 18 Aug 2014

Student		Venkatesh Shukla	venkatesh.shukla.eee11@iitbhu.ac.in
Mentor		Anton Lundin		glance@acc.umu.se
Organisation	Subsurface		https://subsurface.hohndel.org
```

This folder contains the work undertaken by Venkatesh Shukla for Google Summer
of Code '14 under the organisation subsurface and mentorship of Anton Lundin.

The aim of the project was to enable downloading of dives from a divecomputer on
android. This project is a small part of the overall plan to take subsurface to
android platform.

The work is divided in the following parts

a. **libusb**

For communication with the USB devices on android, libusb library is
needed. But this library does not support android inherently. Some amount of
tweaking was required to make it work on android. The related information and
patches to enable its working on android is included in [libusb
folder](https://github.com/venkateshshukla/gsoc14-deliverables/tree/master/libftdi).

b. **libftdi**

Considerable number of divecomputers are based on ftdi chipset.
Libftdi is a library that is used for exchange of information with ftdi based
devices. Libftdi uses libusb for its interaction with usb connected ftdi chips.
Hence, some changes are needed in order to use libftdi on android. Patches
containing these changes and related information can be found in [libftdi folder](https://github.com/venkateshshukla/gsoc14-deliverables/tree/master/libftdi).

c. **libdivecomputer**

Libdivecomputer is a library that is used by subsurface to
extract information from various divecomputers. It is a cross platform library
and uses various interface provided by different platforms for its communication
with divecomputers. For instance, it uses tty on linux platform. On android,
even though it is linux based, tty are not available. Hence, for communication
with various divecomputer, their chipset specific implementation is needed.
During the GSOC period, I have implemented this for ftdi devices using libftdi
library. Patches containing these changes and related information can be found
in [libdivecomputer folder](https://github.com/venkateshshukla/gsoc14-deliverables/tree/master/libdivecomputer).

d **subsurface-android-qt**

Qt owing to its cross platform support can be used to compile on subsurface as
well. This has been done Anton in his [git
repo](https://github.com/glance-/subsurface-android). In order to have this
application use the Android USB APIs, some changes were needed for subsurface.
These changes are explained and patches attached in [subsurface-android-qt folder](https://github.com/venkateshshukla/gsoc14-deliverables/tree/master/subsurface-android-qt).

e. **subsurface-android-jni**

For building subsurface for android as a library without its Qt dependencies,
some changes were essential to subsurface library. These are essentially the
divecomputer.cpp file implementation plus implementation of missing functions.
The changes and patches can be found in [subsurface-android-jni
folder](https://github.com/venkateshshukla/gsoc14-deliverables/tree/master/subsurface-android-jni).

f. **subsurface-android-downloader**

This is an android application that implements the downloadfromdivecomputer
function of subsurface. It utilizes subsurface-android-jni mentioned above for
all processing, libdivecomputer for download of data and the tweaked libusb,
libftdi for communication with USB attached FTDI devices.
This application can be found in the [git repo
here](https://github.com/venkateshshukla/subsurface-android-downloader). README
file describing its usage and build instructions can be found in
[subsurface-android-downloader
folder](https://github.com/venkateshshukla/gsoc14-deliverables/tree/master/subsurface-android-downloader).

