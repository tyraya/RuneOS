RuneOS - DIY RuneAudio+R e
---
- For all **Raspberry Pi**: Zero, 1, 2, 3 and 4 (3+: not yet tested but should work)
- Create **RuneAudio+R e** from [**Arch Linux Arm**](https://archlinuxarm.org/about/downloads) latest releases.
- Interactive interface by [**Dialog**](https://invisible-island.net/dialog/)
- Option to pre-configure Wi-Fi connection (headless mode)
- Options to exclude features, it can be as light as possible in terms of build time and disk space.
- Option to run `ROOT` partition on USB drive (good for hard drive)
	- Not suitable for thumb drive
	- May require powered USB hub for hard drive
- Option for USB only - no SD card: (run both `BOOT` and `ROOT` partitions on USB drive)
	- Raspberry Pi: 3 and 2 v1.2 only (4 not yet supported)
	- Boot duration: 10+ seconds longer (detect no sd card > read boot loader into memory > boot)
	- May require powered USB hub for hard drive

**Procedure**
- Prepare partitions
- Create **Arch Linux Arm**
	- Verify partitions
	- Optional - pre-configure Wi-Fi
	- Download and verify
	- Write `BOOT` and `ROOT` partitions
	- Connect PC to Raspberry Pi
- Create **RuneAudio+R e**
	- Select features:
		- Avahi - URL as: runeaudio.local`
		- Bluez - Bluetooth support
		- Chromium - Browser on RPi (Not available for RPi Zero and 1 - too much for CPU)
		- FFmpeg - [Extended decoders](https://github.com/rern/RuneOS/blob/master/ffmpeg.md)
		- hostapd - RPi access point
		- Kid3 - Metadata tag editor
		- Python - Programming language
		- Samba - File sharing
		- Shairport-sync - AirPlay
		- upmpdcli - UPnP
	- Upgrade kernel and default packages
	- Install feature packages and web interface
	- Configure
	- Set default settings

**Need**
- PC
	- Linux - any distro
	- or Linux on USB e.g., [Manjaro](https://itsfoss.com/create-live-usb-manjaro-linux/) - Arch Linux
	- or Linux in VirtualBox on Windows (with network set as `Bridge Adapter`)
- Raspberry Pi
- Network connection to Raspberry Pi 
	- Wired LAN
	- Optional: Wi-Fi (if necessary)
- Micro SD card: 4GB+ for `BOOT` + `ROOT` partitions
- Option 1: Micro SD card + USB drive (run RuneAudio+R from USB drive)
	- Micro SD card: 100MB+ for `BOOT` partition only
	- USB drive: 4GB+ for `ROOT` partition (or USB hard drive with existing data)
	- For hard drives or faster-than-SD-card thumb drives.
	- It takes less than 20 minutes for the whole process on: RPi 4 + solid state drive + decent download speed
- Option 2: USB drive only - no SD card (Boot from USB drive)
---

### Prepare partitions
- On Linux PC

**Micro SD card only**
- Insert micro SD card
- Open **GParted** app (Manjaro root password: `manjaro`)
- `Unmount` > `Delete` all partitions (make sure it's the micro SD card)
- Create partitions:

| No. | Size        | Type    | Format | Label |
|-----|-------------|---------|--------|-------|
| #1  | 100MiB      | primary | fat32  | BOOT  |
| #2  | (the rest)  | primary | ext4   | ROOT  |
	
**Option 1: Micro SD card + USB drive**
- Micro SD card
	- `Unmount` > `Delete` all partitions (Caution: make sure it's the SD card)
	- Create a partition:

| No. | Size        | Type    | Format | Label |
|-----|-------------|---------|--------|-------|
| #1  | 100MiB      | primary | fat32  | BOOT  |

- USB drive - Blank:
	- `Unmount` > `Delete` all partitions (Caution: make sure it's the USB drive)
	- Create partitions:
	
| No. | Size        | Type    | Format | Label |
|-----|-------------|---------|--------|-------|
| #1  | 3500MiB     | primary | ext4   | ROOT  |
| #2  | (the rest)  | primary | ext4   | (any) |
	
- or USB drive - with existing data:
	- No need to reformat or change format of existing partition
	- Resize the existing to get 3500MiB unallocated space (anywhere - at the end, middle or start of the disk)
	- Create a partition in the space
		
| No.   | Size        | Type    | Format | Label |
|-------|-------------|---------|--------|-------|
| (any) | (existing)  | primary | (any)  | (any) |
| (any) | 3500MiB     | primary | ext4   | ROOT  |
			
**Option 2: USB drive only**

- Set boot bit on Raspberry Pi
	- 3 and 2 v1.2 only
	- 3+ already set
	- 4 not yet supported
- Micro SD card can still be used to boot if inserted.
```sh
# on RuneAudio
echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt

# reboot
shutdown -r now

# verify boot bit = 17:3020000a
/opt/vc/bin/vcgencmd otp_dump | grep 17:
```

- Set partitions (Drive with existing data must be resized and rearranged respectively.)

| No. | Size        | Type    | Format | Label |
|-----|-------------|---------|--------|-------|
| #1  | 100MiB      | primary | fat32  | BOOT  |
| #2  | 3500MiB     | primary | ext4   | ROOT  |
| #3  | (the rest)  | primary | ext4   | (any) |

	
### Create Arch Linux Arm
- Open **Files** app 
- Click `BOOT` and `ROOT` to mount
- Note each path for next confirmation
	- Manjaro - in location bar 
	- Others - hover mouse over `BOOT` and `ROOT`
```sh
# switch user to root
su

# get script and run
wget -qN https://github.com/rern/RuneOS/raw/master/usr/local/bin/create-alarm.sh; chmod +x create-alarm.sh; ./create-alarm.sh
```
- Errors or too slow download: press `Ctrl+C` and run `./create-alarm.sh` again
- Follow instructions until PC to Raspberry Pi connection is up.
- At connecting propmt: confirm `yes` and password `alarm`

### Create RuneAudio+Re
```sh
# switch user to root
su
# password: root

# run script
create-rune.sh
```
- Errors or too slow download: press `Ctrl+C` and run `create-rune.sh` again
- Notification shows when finished.

---

### Optional
[**Create image file**](https://github.com/rern/RuneOS/blob/master/imagefile.md)  

**Setup Wi-Fi auto-connect** (if not set during build)
- On Linux or Windows
- Insert micro SD card
- In `BOOT`
	- Open file `wifi0` with text editor
	- Replace `"NAME` and `PASSWORD` with ones for your Wi-Fi
	- If security of your Wi-Fi is `wep`, replace `wpa` as well.
	- Save as `wifi`
- Move micro SD card to Raspberry Pi
- Power on
