How to Install Windows 7, the hack way
========================================================

# The normal way

 * use a usb-stick or CD-ROM installer
 * install win7
 * apply patches using windows updates

However, it costs hours, it's so clumsy that win7 do not apply patches in one time,
but download and install and reboot for serveral times.

Also some device drivers need to be installed manually.


# The easy way to dupliate a win7 installation.

 * copy windows system partition into a new partition(on same disk or new disk).
 * fix it (describe later)
 * done

## details
 * Scene 1 , the old disk is going to break, you need to move windows system into a new disk (maybe faster SSD)
 * Scene 2 , you need a new windows 7 system for the new machine.

### Step 1

copy the whole windows system partition(call it "System partition" later) to new target
 ```
 	I do it in linux,
 	cp -rp Win7Partition/* NewWin7Partition
 ```

### Step 2

you need a bootable windows partition(call it "bootmgr" later)
  * windows 7 use a 100MB partition as "bootmgr",
  * the size can be larger or smaller
  * actually it can be on the same partition as "System partition",
  * "System partition" can be on an extended partition, but "Bootmgr" must be on the primary partition(because win7's foolish).
  I'm talking about the traditional partition table, the are only 4 slot,
  alloc it as 4 primary
  or 3 primary + 1 extended (which contains infinity number of partition in theory but actually OS/bootloader will panic when you alloc too many)
  * the partition "bootmgr" on, need tagged as "active", do it use fdisk (command a , toggle as bootable) or other tools like diskpart(on win7 installer disk)
  * "bootmgr" need a bootloader program called bootmgr, which is a hidden file in root directory.
    it can be created by `bootsect /nt60 all /force` (or maybe just copy the file)
    it is safe to execute the command because it only affect dos/ntfs partition, your linux ext4 partition will stay safe, and
    it is about to copy file, and will not write to MBR actually, unless you add "/mbr" option.
  * bootmgr install need a BCD config, stored in BCD folder on the same partition of bootmgr file.
  which can be installed by 'bcdboot x: /s y:', you can delete old BCD folder, so that the command can create a new "correct" one for you.
  * the BCD can be viewed using `bcdedit /store file /enum /v` or be edited if necessary.
  * BCD is same role as grub.cfg file in GRUB, it can load multipy windows on different locations, but it cannot load linux I guess. 

## Conclusion 
  the keys are 
  * (1)A primary partition flagged as "active"
  * (2) "bootmgr" single file on the primary partition,
  * (3) BCD directory with correct config on the same partition of "bootmgr"

  In the end, you need a working bootloader, which is the true bootloader which installed physically on the the first sector of the disk.
  which can be a windows one(designed to load bootmgr), or GRUB2(linux way, using "chainloader +1" to load bootmgr)

The process is:
  * BIOS of motherboard load bootloader from first sector of disk
  * bootloader loads itself then bootmgr
  * bootmgr check active flag
  * bootmgr load BCD config
  * bootmgr load \windows\winload.exe(on which partition is according to UUID in BCD config file) to start windows.


If you get more troubles, try using the follow tools:
 * bootsect
 * bootrec
 * bcdboot
 * bcdedit

If you also use Linux, you may also need
 * grub-install
 * update-grub



please comment on http://neoe-code.blogspot.com/2015/01/how-to-install-windows-7-hack-way.html




