## Tutorial

### Get Windows for arm64 
https://uupdump.net/
+ !!!! only choose one Edition. Windows Home OR Pro when you are asked.

### Prepare a bootable USB stick
https://rufus.ie/en/
+ Its simpler to flash the iso as it is, to the usb
+ You can then add the drivers to the usb separately

### What we will be modifying
+ "...\sources\install.wim"                   
+ "...\sources\boot.wim"
+ "...\windows\system32\recovery\winre.wim"   <-- this one is inside install.wim

I unzipped the drivers from this repo to 'c:\drivers' and flashed the windows iso from uupdump via rufus do an USB stick called D:\ in my case.

### Check your USB

```cmd
Dism /Get-ImageInfo /imagefile:"D:\sources\install.wim"
```
should display
```
Deployment Image Servicing and Management tool
Version: 10.0.22621.1

Details for image : D:\sources\install.wim

Index : 1
Name : Windows 11 Pro
Description : Windows 11 Pro
Size : 17,966,528,430 bytes

The operation completed successfully.
```
You can see that i only have windows 11 pro. If you have more that one, you need to change the "index" in the following mount commands, to add the drivers to the edition you would like to install.

### Create mountpoints
```cmd
md C:\mount\install
md C:\mount\boot
md C:\mount\winre
```

### Put the drivers somewhere
I placed the drivers folder from this repo in "C:\\"

### Mount images
```cmd
Dism /Mount-Image /ImageFile:"D:\sources\install.wim" /Index:1 /MountDir:"C:\mount\install"
Dism /Mount-Image /ImageFile:"D:\sources\boot.wim" /Index:2 /MountDir:"C:\mount\boot"
Dism /Mount-Image /ImageFile:"C:\mount\install\windows\system32\recovery\winre.wim" /Index:1 /MountDir:"C:\mount\winre"
```

### Install drivers
run in powershell
```powershell
Add-WindowsDriver -Path "C:\mount\install" -Driver "C:\drivers" -Recurse
Add-WindowsDriver -Path "C:\mount\boot" -Driver "C:\drivers" -Recurse
Add-WindowsDriver -Path "C:\mount\winre" -Driver "C:\drivers" -Recurse
```

### Unmount images
```
Dism /Unmount-Image /MountDir:C:\mount\winre /Commit
Dism /Unmount-Image /MountDir:C:\mount\boot /Commit
Dism /Unmount-Image /MountDir:C:\mount\install /Commit
```

### You are done

### (optional) Do the above steps just with install.wim again, but now using index 1
index 2 is the windows installer.  
index 1 is the WinPE enviroment.
```
Dism /Mount-Image /ImageFile:"D:\sources\boot.wim" /Index:1 /MountDir:C:\mount\boot
Add-WindowsDriver -Path "C:\mount\boot" -Driver "C:\drivers" -Recurse
Dism /Unmount-Image /MountDir:C:\mount\boot /Commit
```

## Other stuff

### Commands
```powershell
# infos like index
Dism /Get-ImageInfo /imagefile:"<path>.wim"
# mount
Dism /Mount-Image /ImageFile:"<path>.wim" /Index:<integer> /MountDir:"<folder-path>"
# add drivers
Add-WindowsDriver -Path "<destination>" -Driver "<source>" -Recurse
# Save unmount
Dism /Unmount-Image /MountDir:"<mountpoint>" /Commit
# Discard unmount
Dism /Unmount-Image /MountDir:C:\mount\winre /discard
```

### (optional) Repacking windows files plus added drivers as ISO
[ADK - Download](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install)
+ select "Deployment Tools" inside the installer.
```cmd
'C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\amd64\Oscdimg\oscdimg.exe' -u2 -m -b"c:\windows_iso_folder\boot\etfsboot.com" "C:\windows_iso_folder" "C:\windows_galaxybook_s.iso"
```
