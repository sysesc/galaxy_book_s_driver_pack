See also [this](https://github.com/mordaut97/galaxy_book_s_driver_pack/issues/1#issuecomment-1446087066
) comment by oktaykarakiya for another way to get this laptop up and running.

## Tutorial creating a fresh arm64 iso usb with drivers

### Get Windows for arm64 
+ [https://uupdump.net/](https://uupdump.net/)
+ Choose only choose one Edition. Windows Home OR Pro.
+ Download the zip, extract is into a folder and run the .bat file inside to create the .iso

### Use Rufus to write the resulting .iso to a flashdrive
+ [Download](https://rufus.ie/en/)


### Check your USB

+ I put the drivers folder from this repo to "C:\\drivers".
+ In this example the USB stick is called "D:\\"
  + If your USB drive letter differs, you need to change the commands accordingly

Open powershell as administrator and try this command.
```cmd
Dism /Get-ImageInfo /imagefile:"D:\sources\install.wim"
```
It should display:
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

As you can see i only have windows 11 pro. If you have more than that just continue as normal, but when installing windows later, choose the windows edition that is displayed as having the index 1 here.

All following commands can be run in powershell as administrator

### Create the mountpoints
```cmd
md C:\mount\install
md C:\mount\boot
md C:\mount\winre
```

### Mount images
```cmd
Dism /Mount-Image /ImageFile:"D:\sources\install.wim" /Index:1 /MountDir:"C:\mount\install"
Dism /Mount-Image /ImageFile:"D:\sources\boot.wim" /Index:2 /MountDir:"C:\mount\boot"
Dism /Mount-Image /ImageFile:"C:\mount\install\windows\system32\recovery\winre.wim" /Index:1 /MountDir:"C:\mount\winre"
```

### Install drivers
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
Reboot and try the usb.

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
- Download and install ADK
  - [ADK - Download](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install)
  - select "Deployment Tools" inside the installer.
- Extract the windows ISO somewhere or use the existing usb stick with the added drivers. Like "C:\windows_iso_folder"
- Add the drivers, if you have not already, like above
- run command below
```cmd
"C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\amd64\Oscdimg\oscdimg.exe" -u2 -m -b"c:\windows_iso_folder\boot\etfsboot.com" "C:\windows_iso_folder" "C:\windows_galaxybook_s.iso"
```
