<body style="background-color:rgb(40, 44, 52);color:lightblue;">

v1.0 - Author: 4ctiv

## Hey, for everybody to seeks to modify his/her UMIDIGI S5 Pro here are my resurces to install a GSI, Root, TWRP:

### TWRP:
  - **Bootable TWRP Github repo** (work in progress and seeking for help) [by lopestom]: [Repository Link](https://github.com/lopestom/device_Umidigi_S5-Pro)
  - How to flash TWRP:
	 - 1.0) **Download** TWRP recovery.img file from release page: [Umidigi S5 Pro specific TWRP](https://github.com/lopestom/device_Umidigi_S5-Pro/releases/)
	 - 1.1) **Download Device rom**: (This) forum post's OTA release: [ROM Link](http://www.mediafire.com/file/feccck129v5mldc/UMIDIGI_S5Pro_20210107.V3.04.rar)
	 - 1.1) **Enable Developer options**: System Settings -> About Phone -> [Tap rappitly on] Build number
	 - 1.2) **Enable ADB**: System Settings -> System -> [Advanced] Developer Options -> USB debugging [Switch to ON]
	 - 1.3) **Enable OEM Unlock**: Settings -> System -> [Advanced] Developer Options -> OEM unlocking [Switch to ON]
	 - 2.0) **Reboot into fastboot**: [Turn of device] -> [Turn On device while pressing volume up (keep pressing volume up untill a text meun shows)] -> [Select] "fastboot" (Use vol+/- to navigate & power Button to select) -> [It should state "fastboot ..." now]
	 - 2.1) **Unlock Bootlader**: [Run] fastboot flashing unlock -> [Displays message on phone, select Yes]
	 - 2.2) ** Flash vbmeta**: [Run] fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img (vbmeta.img found in OTA release download) (Needs to be placed in adb folder)
	 - 2.3) **Flash recovery.img (TWRP rom)**: fastboot flash recovery magisk_patched.img (vbmeta.img found in TWRP Github release download) (Needs to be placed in ADB folder)
	 - 2.4) **Reboot device**: [Run] fastboot reboot
	Done!

### Magisk:
  - Guide (by starterzerosava): [XDA Guide](https://forum.xda-developers.com/t/guide-rooting-the-umidigi-s5-pro-magisk.4151183/)
	 - Does not work with GSI Android 11 (bootloops, known issue for Magisk on Android 11)
       - fix magisk + GSI flashing like this [XDA Guide](https://forum.xda-developers.com/t/tutorial-magisk-on-gsi-on-devices-with-dynamic-partition.4311045/)

### GSI:
  - List of well known GSI's: [List Link](https://github.com/phhusson/treble_experimentations/wiki/Generic-System-Image-(GSI)-list)
	 - Use "arm64" & "A/B" GSI's
  - Working GSI for me (Android 11) AOSP 11.0 v305 system-roar-arm64-ab-gapps.img : [Rom Link](https://github.com/phhusson/treble_experimentations/releases/download/v305/system-roar-arm64-ab-gapps.img.xz)
	 - All seems to work fine except Fingerprint sensor.
  - Install GSI (Generall guide by kusti420): [XDA Guide](https://forum.xda-developers.com/t/how-to-flash-gsis-on-devices-with-dynamic-super-partition.4256667/)
  - Fix "Device not Certified" Error: [XDA Guide](https://www.xda-developers.com/how-to-fix-device-not-certified-by-google-error/) (also needed to silent + minimize the notifications)
	  - one time in his code he wrote Fastboot, use fastboot instead for it to work
    - instead of rebooting into recovery and factory reset you can use: " fastboot -w " it will wipe data & cache
	 - WARNING: Please remember to backup your data, especally after step 13 all your personal data will be whiped in the install process

I hope you found this summary usefull 😁.

### ---------------------------- Example ----------------------------

> adb devices #CHECK IF YOU SEE A (YOUR) DEVICE

> adb reboot bootlaoder #PHONE REBOOTS
>
> fastboot devices #CHECK IF YOU SEE A (YOUR) DEVICE
>
> fastboot flashing unlock #NOTE: ON PHONE ACCEPT UNLOCK (VOL UP)
>
> fastboot flashing unlock_critical #NOTE: ON PHONE ACCEPT UNLOCK (VOL UP)

> fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
>
> fastboot --disable-verity --disable-verification flash boot magisk_patched.img
>
> fastboot --disable-verity --disable-verification flash recovery twrp_recovery.img
>
> fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
>
> fastboot reboot fastboot #PHONE REBOOTS
>
>> - fastboot flash product product.img
>>
>>   OR
>>
>> - fastboot delete-logical-partition product
>> - fastboot create-logical-partition product 400000000
>
> fastboot flash system gsi_system.img
>
> fastboot -w #WARNING: THIS DELETES ALL USERDATA
>
> fastboot reboot #PHONE REBOOTS

```shell
adb reboot bootlaoder
fastboot flashing unlock
fastboot flashing unlock_critical
fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
fastboot reboot fastboot
fastboot flash product product.img
fastboot flash system gsi_system.img
fastboot -w
fastboot reboot
```