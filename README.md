GSOC '14 : Subsurface android downloader
----------------------------------------

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
patches to enable its working on android is included in libusb folder.

b. **libftdi**

Considerable number of divecomputers are based on ftdi chipset.
Libftdi is a library that is used for exchange of information with ftdi based
devices. Libftdi uses libusb for its interaction with usb connected ftdi chips.
Hence, some changes are needed in order to use libftdi on android. Patches
containing these changes and related information can be found in libftdi folder.

c. **libdivecomputer**

Libdivecomputer is a library that is used by subsurface to
extract information from various divecomputers. It is a cross platform library
and uses various interface provided by different platforms for its communication
with divecomputers. For instance, it uses tty on linux platform. On android,
even though it is linux based, tty are not available. Hence, for communication
with various divecomputer, their chipset specific implementation is needed.
During the GSOC period, I have implemented this for ftdi devices using libftdi
library. Patches containing these changes and related information can be found
in libdivecomputer folder.
