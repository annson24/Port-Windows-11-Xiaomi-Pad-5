<img align="right" src="https://raw.githubusercontent.com/erdilS/Port-Windows-11-Xiaomi-Pad-5/main/nabu.png" width="425" alt="Windows 11 Running On A Xiaomi Pad 5">


# Running Windows on the Xiaomi Pad 5
> [!WARNING]
> **PLEASE DON'T USE OUTDATED VIDEO GUIDES ON YOUTUBE OR ANY OTHER PLATFORM! THESE VIDEOS ARE OUTDATED AND YOU CAN BRICK YOUR DEVICE USING THEM! IF YOU NEED A VIDEO GUIDE, USE THIS [NEW VIDEO GUIDE](https://www.youtube.com/watch?v=rGPbdFq7gKs) FROM [ArtoSeVeN](https://www.youtube.com/channel/UCYjwfxlYlJ7Nnzv01oszQvA)**

## Installation
> [!NOTE]
> It is recommended to open CMD or powershell as an admin now, and then access the platform-tools folder using the `cd C:\path\to\platform-tools` command, replacing the path with the actual path of the folder.
> Use the same window in the entire guide, do not close it.

### Prerequisites
- ```Brain```

- [```UEFI image```](https://github.com/erdilS/Port-Windows-11-Xiaomi-Pad-5/raw/main/images/xiaomi-nabu_20240212-V2.img)
  
- [```ARM Windows esd```](https://worproject.com/esd) (Select - Version:  ```11``` Build:  ```22631.2861``` Architecture:  ```ARM64``` Edition:  ```CLIENT``` Language:  ```select your language```)
    
- [```Drivers```](https://github.com/map220v/MiPad5-Drivers/releases/latest)

### Boot back into recovery to start installing Windows

```cmd
fastboot boot <recovery.img>
```

#### Execute msc 

> If it asks you to run it once again, do so

```cmd
adb shell msc
```
### Assign letters to disks
  

#### Start the Windows disk manager

> Once the Xiaomi Pad 5 is detected as a disk

```cmd
diskpart
```


#### Assign `X` to Windows volume

#### Select the Windows volume of the tablet
> Use `list volume` to find it, it's the one named "WINNABU"

```diskpart
select volume <number>
```

#### Assign the letter X
```diskpart
assign letter x
```

### Assign `Y` to ESP volume

#### Select the esp volume of the tablet
> Use `list volume` to find it, it's the one named "ESPNABU"

```diskpart
select volume <number>
```

#### Assign the letter Y

```diskpart
assign letter y
```

#### Exit diskpart
```diskpart
exit
```

  
  

### Install

> Replace `<path/to/install.esd>` with the actual path of install.esd

> If you got your Windows image elsewhere (which might also be called `install.wim`), replace `index:6` with `index:1`
```cmd
dism /apply-image /ImageFile:<path/to/install.esd> /index:6 /ApplyDir:X:\
```

### Install Drivers

> You can download the Drivers [here](https://github.com/map220v/MiPad5-Drivers/releases/latest)

> When it ask you to "Enter Drive letter..." type **`X:`**

> Do not run it as an administrator, it will ask for administrator rights when needed.

```cmd
 Open the folder with Drivers and run OfflineUpdater.cmd
```

### Create Windows bootloader files for the EFI
> If an error occurs when copying boot files, check `diskpart` to see if ESPNABU still has letter Y. If it does not, add any other letter (such as K) and replace the Y in the below command with said letter respectively
```cmd
bcdboot X:\Windows /s Y: /f UEFI
```

## Remove the drive letter for ESPNABU to avoid the appearance of a phantom drive letter
> If this does not work, ignore it and skip to the next command. These phantom drives will disappear the next time you reboot your PC.
```cmd
mountvol y: /d
```


## Boot into Windows

### Make a backup of your rooted boot image

```cmd
adb shell "dd if=/dev/block/platform/soc/1d84000.ufshc/by-name/boot$(getprop ro.boot.slot_suffix) of=/tmp/rooted_boot.img" && adb pull /tmp/rooted_boot.img
```

### Reboot to bootloader 

```cmd
adb reboot bootloader
```

### Download and flash the UEFI image
> Download the [UEFI image](https://github.com/erdilS/Port-Windows-11-Xiaomi-Pad-5/raw/main/images/xiaomi-nabu_20240212-V2.img)

```cmd
fastboot flash boot <path to image>
```

## Reboot to Windows
```cmd
fastboot reboot
```

> [!NOTE]
> On the first Windows boot, it will not see any Wi-Fi networks. Restart your tablet by holding down the power button until it restarts. After the reboot, it will be fixed. If you get a pop-up saying "Could not connect", press retry until it works (usually 5 times)

### Boot back into Android
After Windows has been set up, press the restart button in Windows (NOT SHUTDOWN), then as it restarts, hold `volume down` + `power`to reboot back to fastboot
> Use your backup boot image and flash it in fastboot to return to Android

```cmd
fastboot flash boot rooted_boot.img
```

### [Last step: Set up Dualboot](dualboot-en.md)