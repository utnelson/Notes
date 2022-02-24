# Hardware

## Firmware images

```console
$ binwalk firmware.bin

DECIMAL   	HEX       	DESCRIPTION
-------------------------------------------------------------------------------------------------------------------
0         	0x0       	DLOB firmware header, boot partition: "dev=/dev/mtdblock/2"
112       	0x70      	LZMA compressed data, properties: 0x5D, dictionary size: 33554432 bytes, uncompressed size: 3797616 bytes
1310832   	0x140070  	PackImg section delimiter tag, little endian size: 13644032 bytes; big endian size: 3264512 bytes
1310864   	0x140090  	Squashfs filesystem, little endian, version 4.0, compression:lzma, size: 3264162 bytes,  1866 inodes, blocksize: 65536 bytes, created: Tue Apr  3 04:12:22 2012

Extract firmware
$ binwalk -e firmware.bin

```