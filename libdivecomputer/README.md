#Libdivecomputer for Android

Libdivecomputer is a library which is used for communication for various
divecomputers and extract data from them. It is a cross platform library and
works both on windows and posix based linux and mac operating systems. On posix
systems it utilises tty interfaces for communication.

Usage of tty interface crosses a big hurdle on posix systems. The chipset
specific drivers of various divecomputers are already present in the kernel. We
get a unified tty interface to talk with all the chipsets.

On android, the tty interfaces are absent. Hence, in order to facilitate the
communication with libdivecomputer on android, we require chipset specific
implementation of communication.

For this project, I have started with implementation for FTDI chipsets. FTDI
chipsets are present in considerable number of divecomputers. Some examples of
those are Heinrichs Weikamp OSTC, Cressi Leonardo, Oceanic and Suunto devices.
Out of these, Oceanic, Suunto and Cressi uses custom PID for its chipsets. These
can be found [here](http://www.libdivecomputer.org/drivers.html).

For communication on android, serial_ftdi.c script is prepared. It has all the
serial functions implemented specifically for FTDI devices.

###Usage
**serial_ftdi.c**

All the usual functions available for posix and win32 interfaces are implemented
here as well. Some of the crucial points and differences are as follows

1. path to tty device passed as name tp serial_open is irrelevant for ftdi
   backend on android because tty interfaces are not present on android. Instead
   of this, the usb file descriptor obtained from Android USB API's after
   getting permissions from user and opening devices is relevant and would be
   used internally to create libusb_device. Hence the libdivecomputer API's
   would need a little modifications. Rather than passing `const char *name`, it
   is more proper to pass a `const void *params`. The serial_open implementation
   in serial_ftdi.c assumes this modification.

2. As the path to the device is not present, directly opening the device is not
   possible. Hence this script looks for supported FTDI devices by checking for
   VIDs and PIDs (both standard and custom) and opens the first one matching.

3. serial_enumerate - In implementation for posix devices, /dev directory is
   searched for presence of devices matching ttyS, ttyUSB, ttyACM, rfcomm and
   when found, their path is passed to the callback function. For serial_ftdi
   all the ftdi devices with accepted VIDs and PIDs are found and callback
   function called with libusb_device as their first parameter.

4. serial_read - In posix interface, read function of tty is used. It returns 0
   for timeout and can be used for throwing error. But on ftdi device, 0 means
   that no data was available. data could again be available after a short time.
   Hence, it is more prudent to implement an exponential backoff. Waiting time
   increases with successive calls and when its greater than MAX_BACKOFF,
   timeout error is thrown.

###Patches
Given below is the changes made in various patched for usage of libdivecomputer
on android.
The patches can be applied to the libdivecomputer git repo
(git://git.libdivecomputer.org/libdivecomputer) by executing the following
command.
```
$ git am patch_name.patch
```
The changes made can also be found in android branch of my github repo[1].

1. **serial_ftdi.c-for-communication-with-ftdi-devices.patch**
This patch adds the implemented serial_ftdi.c for android.

2. **Check-for-libftdi-include-it-in-Required.private.patch**
This patch adds check for libftdi for building on android. On android
serial_ftdi.c would be used instead of serial_posix.c. It also adds libftdi
as a raquired library when cross compiling for android.

3. **Pass-a-void-instead-of-const-char.patch**
Rather than passing const char \*name, it is better to pass a const void
\*params parameter. This way, both const char \* can be passed for posix as well
as int for ftdi implementations. This patch makes the necessary changes.

4. **Implementation-of-serial_enumerate.patch**
serial_enumerate has been implemented. But passing struct libusb_device \*dev
rather than const char \*. This calls for changes in serial_callback_t. And
hence this patch is not merged with patch 1.

### Known bugs
There are three major bugs known.

1. While opening the ftdi device, the script looks for devices with ftdi VID and
   PID. It starts execution for the first FTDI device encountered. This could be
   very wrong when dealing with multiple devices. On android, in the most common
   cases, only one device is attached. Hence, it would generally work.

2. As file descriptor of underlying USB is first received from android api's and
   then opening takes place as per point 1 above, how can we be sure that the
   device opened from the Android and the matching device found on open are the
   same. This would be wrong in case of multiple devices. Again, for android, it
   generally works because of presence of single attached USB device.

3. Sometimes, race condition is encountered on serial_ftdi. This happens when
   the application is expecting some data but ftdi_read_data returns 0 -
   implying no data available. The application keeps trying again until data is
   received. This bug is bypassed by implementation of exponential backoff.

### Upstream status.
Over the summers, various discussions have taken place with Jef Driesen of
libdivecomputer for android implementation. There is a need for unified
implementation for all communication interfaces like irda, tty, usb and
bluetooth. The serial_ftdi.c implementation was found acceptable to Jef and
could be included as a isolated unreferenced file in libdivecomputer. Inclusion
of this file in build could be done when unified implementation is done.

#####References
1. https://github.com/venkateshshukla/libdivecomputer/tree/android

