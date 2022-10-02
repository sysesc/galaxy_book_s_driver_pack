
### wim images that need to be modified
+ "...\sources\install.wim"                   
+ "...\sources\boot.wim"
+ "...\windows\system32\recovery\winre.wim"   <-- is inside install.wim

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
Dism /Mount-Image /ImageFile:"D:\sources\install.wim" /Index:2 /MountDir:C:\mount\boot
Add-WindowsDriver -Path "C:\mount\boot" -Driver "C:\drivers" -Recurse
Dism /Unmount-Image /MountDir:C:\mount\boot /Commit
```


### Important commands
```powershell
#Sheme
Dism /Mount-Image /ImageFile:"<path>.wim" /Index:<integer> /MountDir:<folder-path>
#Example
Dism /Mount-Image /ImageFile:"D:\sources\install.wim" /Index:1 /MountDir:C:\mount\boot
```
