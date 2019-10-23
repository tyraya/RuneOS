### Image file to USB drive + SD card

**`ROOT` partition**
- Plug in USB drive
  - Blank:
    - Format: `ext4`
    - Label: `ROOT`
  - With existing data:
    - No need to change format of existing partition
    - Resize and create a new 4GB partition (anywhere - at the end, middle or start of the disk)
    - Format: `ext4`
    - Label: `ROOT`
- Click `ROOT` in **Files** to mount
```sh
# install package
apt install kpartx

# function for verify names
cols=$( tput cols )
showData() {
    printf %"$cols"s | tr ' ' -
    echo $1
    echo $2
    printf %"$cols"s | tr ' ' -
}

# map image file
kpartx -av IMAGEFILE.img

# mount ROOT partition
mount /dev/mapper/loop0p2 /mnt

# get ROOT partition and verify
ROOT=$( df | grep ROOT | awk '{print $NF}' )
showData "$( df -h | grep ROOT )" "ROOT = $ROOT"

# copy to usb drive
cp -r /mnt $ROOT

# unmount
umount /mnt
```

**`BOOT` partition**
- Insert Micro SD card
- Format to `fat32` and label as `BOOT`
- Click `BOOT` in **Files** to mount
```sh
# mount BOOT partition
mount /dev/mapper/loop0p1 /mnt

# get BOOT partition and verify
BOOT=$( df | grep BOOT | awk '{print $NF}' )
showData "$( df -h | grep BOOT )" "BOOT = $BOOT"

# copy to usb drive
cp -r /mnt $BOOT

# unmount
umount /mnt
```