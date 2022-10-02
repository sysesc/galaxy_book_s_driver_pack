### Get Windows for arm64 
https://uupdump.net/
### Prepare a USB
https://rufus.ie/en/
+ Its simpler to flash the iso as it is, to the usb
+ You can then add the drivers to the usb separately

### What we will be modifying
+ "...\sources\install.wim"                   
+ "...\sources\boot.wim"
+ "...\windows\system32\recovery\winre.wim"   <-- this one is inside install.wim

### create mountpoints
```cmd
md C:\mount\install
md C:\mount\boot
md C:\mount\winre
```

### mount images
```cmd
Dism /Mount-Image /ImageFile:"D:\sources\install.wim" /Index:1 /MountDir:"C:\mount\install"
Dism /Mount-Image /ImageFile:"D:\sources\boot.wim" /Index:1 /MountDir:"C:\mount\boot"
Dism /Mount-Image /ImageFile:"C:\mount\install\windows\system32\recovery\winre.wim" /Index:1 /MountDir:"C:\mount\winre"
```

### install drivers
```powershell
Add-WindowsDriver -Path "C:\mount\install" -Driver "C:\drivers" -Recurse
Add-WindowsDriver -Path "C:\mount\boot" -Driver "C:\drivers" -Recurse
Add-WindowsDriver -Path "C:\mount\winre" -Driver "C:\drivers" -Recurse
```

### unmount images
```
Dism /Unmount-Image /MountDir:C:\mount\winre /Commit
Dism /Unmount-Image /MountDir:C:\mount\boot /Commit
Dism /Unmount-Image /MountDir:C:\mount\install /Commit
```

### Do the above steps for any install.wim again, but with index 2
```
Dism /Mount-Image /ImageFile:"D:\sources\boot.wim" /Index:2 /MountDir:C:\mount\boot
Add-WindowsDriver -Path "C:\mount\boot" -Driver "C:\drivers" -Recurse
Dism /Unmount-Image /MountDir:C:\mount\boot /Commit
```


### commands
```powershell
Dism /Get-ImageInfo /imagefile:"<path>.wim"
Dism /Mount-Image /ImageFile:"<path>.wim" /Index:<integer> /MountDir:"<folder-path>"
Add-WindowsDriver -Path "<destination>" -Driver "<source>" -Recurse
Dism /Unmount-Image /MountDir:"<mountpoint>" /Commit
```


### Repacking as ISO
```
'C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\amd64\Oscdimg\oscdimg.exe' -u2 -m -b"c:\windows_iso_folder\boot\etfsboot.com" "C:\windows_iso_folder" "C:\windows_galaxybook_s.iso"
```
