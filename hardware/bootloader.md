# Bootloader

The GHI Bootloader is used to update the firmware on our devices. It is the first program to run and unless the device specific LDR pins are set (see device documentation for details), it will execute the firmware on the device if present. The bootloader communicates over a USB virtual serial port or a regular serial port. The interface used is usually controlled by a MODE pin. See your device specifications for details on interface configuration and selection and for which version of the bootloader it runs.

## Version 2.0
All commands and results are terminated with CR and LF (\r\n). "OK." will be sent after each successful command.

On startup, a banner is sent that is terminated by "OK.". Once the banner is received, you are free to enter any of the case-insensitive single-character commands described below.

Most commands require confirmation. Send Y or y followed by a new-line to proceed or anything else to cancel.

- V: returns the current version.
- N: returns the current device type.
- E: erases all user sectors of the device.
- R: runs the firmware if present.
- B: increases the baud rate in serial mode to 921,600.
- X: upload a file to the device using 1K XMODEM. Only send *.ghi files meant for your device.
- U: upload a file to the device using 1K XMODEM. Only send *.glb files meant for your device.

## Version 1.0
Currently the EMX, G120, and G120E use this version of the bootloader. All results are terminated with LF (\n). Commands are executed as soon as they entered without waiting for a new-line. "BL" or "Done." will be sent after each command.

On startup, a banner is sent that is terminated by "BL". Once the banner is received, you are free to enter any of the case-sensitive single-character commands described below.

- V: returns the current version.
- E: erases all user sectors of the device (* is sent while erasing).
- R: runs the firmware if present.
- B: increases the baud rate in serial mode to 921,600.
- X: upload a file to the device using 1K XMODEM. Only send *.ghi files meant for your device. The firmware is automatically run after a successful upload.

# GLB File Format

The glb files that are loaded onto devices have some additional metadata that help the bootloader function in addition to the raw data itself. The first 1,024 bytes of a glb file is the upload header. Starting from offset 0 are the below fields. The rest of the header is currently reserved.

1. 32 bit signature number that is unique for each device.
2. 32 bit unsigned address in flash that this image should be copied to.
3. 32 bit unsigned length of the image to flash rounded to the nearest 1,024 bytes.
4. 16 bit CRC-CCITT of the image.

After the upload header is the actual image to flash. If its length is not divisible by 1,024 bytes, it is padded until it is. For images that are meant to be bootable, the address in the upload header should be set to the entry point defined for the specific device. Bootable images have an additional 1,024 byte header at the beginning of the image that is used to verify the image before booting it. The boot image is also padded to the nearest 1,024 bytes. Starting from offset 0 are the below fields. The rest of the header is currently reserved.

1. 32 bit signature number that is unique for each device.
2. 32 bit unsigned address in flash that is the entry point the bootloader will invoke.
3. 32 bit unsigned length of the boot image rounded to the nearest 1,024 bytes.
4. 16 bit CRC-CCITT of the boot image bounded by the specified address and length.