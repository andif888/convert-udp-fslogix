# Migrate UPD Profiles to FSLogix 

## Pre-requesites

AD Powershell Modules

```powershell
Install-WindowsFeature RSAT-AD-PowerShell
```

## Migrate UPD VHDs to FSLogix VHDs

1. Copy UVHDs to new folder and share folder e.g to `\\fileserver01\uvhd_profiles`
2. Create new folder and share folder e.g. at `\\fileserver01\fslogix_profiles` 
3. Adjust variables in [Convert-UPDtoFSLogix.ps1](Convert-UPDtoFSLogix.ps1)  
  ```powershell 
   set $updroot = "\\fileserver01\uvhd_profiles"  
   set $fslogixroot = "\\fileserver01\fslogix_profiles"  
   set $errorlogfolder = "\\fileserver01\uvhd_profiles"  
   ```
4. Run [Convert-UPDtoFSLogix.ps1](Convert-UPDtoFSLogix.ps1) in ISE as Administrator

## Disable User Profile Disks on all RDSH

Use Server Manager to disable User Profile Disks on all RDSH.

Or use powershell  
 
Example: 
```powershell
Set-RDSessionCollectionConfiguration -CollectionName "Session Collection 02" -DisableUserProfileDisk -ConnectionBroker "RDCB.Contoso.com"
``` 

## Install and Configure FSLogix

Download and install FSLogix   
[https://aka.ms/fslogix_download](https://aka.ms/fslogix_download)


### Configure FSLogix on all RDSH

Example: 
```
[HKEY_LOCAL_MACHINE\SOFTWARE\FSLogix\Profiles]
"Enabled"=dword:00000001
"VHDLocations"="\\fileserver01\fslogix_profiles"
"VolumeType"="VHDX"
"SizeInMBs"=dword:00000c00
"IsDynamic"=dword:00000001
"LockedRetryCount"=dword:00000018
"LockedRetryInterval"=dword:00000006
``` 

[FSLogix Documentation](https://docs.microsoft.com/en-us/fslogix/)


## Optional - Convert VHD to VHDX



### Pre-Requesites

Check exisiting
```powershell
Get-WindowsFeature *hyper-v*
```

Hyper-V-Powershell
```powershell
Install-WindowsFeature -Name Hyper-V-PowerShell
```

Convert VHD to VHDX
```powershell
Convert-VHD TestVHD.vhd -VHDFormat VHDX -DestinationPath C:\temp\VHDs\TestVHDX.vhdx -DeleteSource 
```

## Reference

The script is a modified version from Roger Critz from the [Microsoft Tech Community Post](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/convert-upd-to-fslogix-container/m-p/927214)