# Bitlocker
Sega Nu uses the USB key and Bitlocker to protect its storage. During investigation the main system drive has two bitlocker partitions protected by Startup Key + TPM.

### Encryption on System Disk
It appears that the system uses Startup Key + TPM for the system disks. There are two partition encrypted using bitlocker (/dev/sdb2, /dev/sdb3).

![](../res/mdsf5.gif)

Credit: [petje](http://www.emuline.org/topic/1695-arcade-pc-chunithm-amazon-v130-omnimix-sega-nu-11/?do=findComment&comment=65867)

Extraction using bitlocker2john or violatile framework is not possible. DMA attack or TPM sniffing will be used to extract the FVEK.


### Encryption on Data Disk
The appears the data disk have 1 bitlocker encrypted partition, and couple other normal partition. One of the normal parition appears to include some game data.