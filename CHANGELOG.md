# Changelog

All notable changes to this project will be documented in this file.

This changelog is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/).
This project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

## [0.0.1] - 2019-12-25

### Added

- Started a README(.md)
- Started this CHANGELOG(.md) to journal about my journey
- Added a `.gitignore` file to ignore my local `downloads/` directory
- Built a SmartOS Bootable USB Key following [this guide][0.0.1-1]

  Downloaded the [latest SmartOS][0.0.1-2] tarball... ran some commands I found
  on the internet (what's the worst that could happen???):

  ```
  $ diskutil list
  /dev/disk0 (internal, physical):
     #:                       TYPE NAME                    SIZE       IDENTIFIER
     0:      GUID_partition_scheme                        *1.0 TB     disk0
     1:                        EFI EFI                     314.6 MB   disk0s1
     2:                 Apple_APFS Container disk1         1.0 TB     disk0s2

  /dev/disk1 (synthesized):
     #:                       TYPE NAME                    SIZE       IDENTIFIER
     0:      APFS Container Scheme -                      +1.0 TB     disk1
                                   Physical Store disk0s2
     1:                APFS Volume Macintosh HD - Data     241.7 GB   disk1s1
     2:                APFS Volume Preboot                 125.5 MB   disk1s2
     3:                APFS Volume Recovery                1.6 GB     disk1s3
     4:                APFS Volume VM                      3.2 GB     disk1s4
     5:                APFS Volume Macintosh HD            11.0 GB    disk1s5

  /dev/disk2 (external, physical):
     #:                       TYPE NAME                    SIZE       IDENTIFIER
     0:     FDisk_partition_scheme                        *8.0 GB     disk2
     1:             Windows_FAT_32 NO NAME                 8.0 GB     disk2s1

  $ diskutil unmountDisk /dev/disk2
  Unmount of all volumes on disk2 was successful
  $ gunzip smartos-20191219T093118Z-USB.img.gz
  $ ls
  smartos-20191219T093118Z-USB.img
  $ sudo dd bs=1m if=smartos-20191219T093118Z-USB.img of=/dev/rdisk2
  1907+1 records in
  1907+1 records out
  2000000000 bytes transferred in 157.314476 secs (12713388 bytes/sec)
  $ diskutil eject /dev/disk2
  Disk /dev/disk2 ejected
  ```

  Success!

- Next: Stick the USB key in a port and boot from it!


[0.0.1-1]: https://wiki.smartos.org/creating-a-smartos-bootable-usb-key/
[0.0.1-2]: https://us-east.manta.joyent.com/Joyent_Dev/public/SmartOS/latest.html

## [0.0.0-2] - 2019-12-25 – "The Prequel"

- No longer able to run all-the-containers on my laptop, I suddenly remembered
  that I have an unused computer on the shelf (reaches for the Intel NUC).

- Installed some DDR4 RAM and NVMe storage in an Intel NUC, turned it on...

  ```
  A bootable device has not been detected.
  ```

  Womp womp.

- Started Googling...

- Quickly determined that it would be helpful to document this journey.

  ```
  $ mkdir homelab && cd homelab && git init
  ```

## [0.0.0-1] – 2019-??-?? – "Failure to Launch"

- Unboxed an Intel NUC, realized it required assembly, set it on a shelf to
  collect dust.
