# Motherboard

The motherboard is a custom moderboard with unknown bios (haven't powered on to test it yet), it only has two Pcie slots and two DIMM slots.

![](./../res/069DA365-A2BC-4F0A-8B0B-BFD462A16C4A.jpeg)

## Memory
The motherboard has two 2GB DDR3 1600 from Transcend
![memory](./../res/07DECB28-0FC9-44CF-8704-D28BEBE7246E.jpeg)


## TPM
The motherboard include an TPM chip for disk encryption, 2 partitions on the System Drive is encrypted with bitlocker and 1 partition on the Data Drive is encrypted with bitlocker, it is believed they all use different FVEK for the encryption.
![tpm chip](../res/B91263CA-837D-4663-B194-D6218B866B75.jpeg)

The TPM chip used is SLB96350 TPM 1.2 device. TPM LPC sniffing attack is possible to extract the FVEK keys. (Due to the complex solderng required, I might fail extracting the key, but it totally doable)

* [TPM key extraction](https://pulsesecurity.co.nz/articles/TPM-sniffing)

## IO
This motherboard uses an [Nuvoton NCT6776F](https://static6.arrow.com/aropdfconversion/b19813efc8e1635369c0c75d2e50037b046c2513/nct6776f_nct6776d_datasheet_v1_2.pdf) for LPC IO

![io chip](../res/F48B3D4F-F2F8-4BF8-A7EB-D6B73256EEC4.jpeg)

It also got a [Fintek F81866A](https://www.fintek.com.tw/index.php/products/i-o-controller-and-i-f-management/item/59-f81866a) for parallel, uart, etc

![io chip 2](./../res/A0B3B7D6-D53E-4C35-9E6A-815DDBDD8443.jpeg)

## Ethernet
The enternet controller on this motherboard is Intel's [82583V](https://ark.intel.com/content/www/us/en/ark/products/41676/intel-82583v-gigabit-ethernet-controller.html) 
![ethernet controller](../res/D43B97FC-11ED-4BC0-9472-AC9979AF5077.jpeg)

## Sound
The sound chip used is Realtek's [ALC886](https://www.realtek.cz/download-ALC886-sound-driver-for-Windows10-64bit.html)

![sound chip](../res/F2AF56D6-E15B-4974-B7F2-28F670D98587.jpeg)
