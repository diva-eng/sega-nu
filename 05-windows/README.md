# Windows
The system runs Windows 8.1 Embedded Industry Standard

## Users
### AppUser
* Auto logged in after boot, will execute `C:\Windows\SEGA\System\sgpreboot.exe` after login
* Has only Network Configuration privileges

### SystemUser
* Password protected
* Administrator user
* Used in `C:\Windows\SEGA\System\sgpreboot.exe` to execute `sgboot.exe`
* Password can be found under `C:\Windows\SEGA\tmp\AUTOUNATTEND.XML`


## Services
### MMCSS
[MMCSS](https://docs.microsoft.com/en-us/windows/win32/procthread/multimedia-class-scheduler-service) is used to give multimedia more CPU time in scheduling to ensure multimedia performance (makes sense when the windows explorer runs so slowly)
### EWF
EWF is a write filter that protects the OS drive and other drives from tamper, will redirect all writes to memory.
### Keyboard Filter
Just an ordinary filter to filter out breakout keys
### Gesture Filter
Same as the keyboard filter, but for filtering windows 8 gestures


## Decoding SystemUser's password

```
 > $encryptedpassword = "PASSWORD FOUND IN XML"

 > $dPwD= ([system.text.encoding]::Unicode.GetString([system.convert]::Frombase64string($encryptedpassword)))

 > write-host $dPwD.Substring(0,($dPwD.length-8))
```

## AUTOUNATTEND.XML
Found under `C:\Windows\SEGA\tmp`, looks like it include the settings used to provision the machine in factory, it also incldue account information and service configuration.

Example:
```xml
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:ew="urn:schemas-microsoft-com:embedded.unattend.internal.v2">
...
    <settings pass="windowsPE">
        <component name="Microsoft-Windows-International-Core-WinPE" processorArchitecture="amd64" publicKeyToken="" language="neutral" versionScope="nonSxS" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <SetupUILanguage>
                <WillShowUI>OnError</WillShowUI>
                <UILanguage>en-US</UILanguage>
            </SetupUILanguage>
            <InputLocale>en-US</InputLocale>
            <LayeredDriver>1</LayeredDriver>
            <SystemLocale>en-US</SystemLocale>
            <UILanguage>en-US</UILanguage>
            <UILanguageFallback>en-US</UILanguageFallback>
            <UserLocale>en-US</UserLocale>
        </component>
        <component name="Microsoft-Windows-International-Core-WinPE" processorArchitecture="x86" publicKeyToken="" language="neutral" versionScope="nonSxS" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <SystemLocale />
        </component>
        <component name="Microsoft-Windows-Setup" processorArchitecture="amd64" publicKeyToken="" language="neutral" versionScope="nonSxS" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <DiskConfiguration>
                <Disk wcm:action="add">
                    <ModifyPartitions>
                        <ModifyPartition wcm:action="add">
                            <Active>false</Active>
                            <Extend>false</Extend>
                            <Format>NTFS</Format>
                            <Label>WES8</Label>
                            <Letter>C</Letter>
                            <Order>2</Order>
                            <PartitionID>2</PartitionID>
                            <TypeID>0x7</TypeID>
                        </ModifyPartition>
                        <ModifyPartition wcm:action="add">
                            <Active>true</Active>
                            <Format>NTFS</Format>
                            <Label>System</Label>
                            <PartitionID>1</PartitionID>
                            <TypeID>0x7</TypeID>
                            <Order>1</Order>
                        </ModifyPartition>
                        <ModifyPartition wcm:action="add">
                            <Active>false</Active>
                            <Extend>false</Extend>
                            <Format>NTFS</Format>
                            <Label>AMFS</Label>
                            <Order>3</Order>
                            <Letter>D</Letter>
                            <PartitionID>3</PartitionID>
                            <TypeID>0x7</TypeID>
                        </ModifyPartition>
                    </ModifyPartitions>
                    <DiskID>0</DiskID>
                    <WillWipeDisk>true</WillWipeDisk>
                    <CreatePartitions>
                        <CreatePartition wcm:action="add">
                            <Order>1</Order>
                            <Size>350</Size>
                            <Type>Primary</Type>
                        </CreatePartition>
                        <CreatePartition wcm:action="add">
                            <Order>2</Order>
                            <Size>20480</Size>
                            <Type>Primary</Type>
                        </CreatePartition>
                        <CreatePartition wcm:action="add">
                            <Order>3</Order>
                            <Size>27648</Size>
                            <Type>Primary</Type>
                        </CreatePartition>
                    </CreatePartitions>
                </Disk>
            </DiskConfiguration>
            <ImageInstall>
                <OSImage>
                    <InstallTo>
                        <DiskID>0</DiskID>
                        <PartitionID>2</PartitionID>
                    </InstallTo>
                    <InstallToAvailablePartition>false</InstallToAvailablePartition>
                </OSImage>
            </ImageInstall>
        </component>
    </settings>
    ...
    <ew:answerFileInfo processorArchitecture="amd64" osVersion="6.2.9200.16384" />
</unattend>
```