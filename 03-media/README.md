# Media

The game install media uses a traditional DVD-ROM disk (probably double layered). You are able to read the content of the disc if you have the disk media, but without keychip and main application, it is unknown if you can dump the contents.


## Picture

Hatsune Miku Project Diva Arcade Future Tone
![DVD photo](../res/DF9B2152-A2DE-412B-BB83-7F04F0E13F8F.jpeg)

The DVD patch I acquired is for one version of the game, and it contains the following files.

```
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
--r---         5/29/2015   2:09 AM      722731008 AAV_0039.70.01_20150428165732_0.pack
--r---         5/29/2015  10:41 AM              0 DISC_01_03
--r---         5/29/2015  10:38 AM     7663517696 SBZV_5.00.02_20150529201945_0_0_29234.app
--r---         5/29/2015  10:36 AM            192 SBZV_AAV.icf
```

After checking against DVD for another version of the game, it looks like SEGA NU (ALL P-ras) DVD-ROM SYSTEM uses the following files as install:

**AAV_0039.70.01_20150428165732_0.pack**

AAV stands for the Nu-series machines based on: [SegaTools](https://github.com/nzgamer41/SegaToolsTP/blob/26b34c527f841c104ba735afdb59e6b8dba8c43f/doc/config/common.md#serialno), it is also the start of the serial number of the machine. 

**DISC_01_03**

This looks like an empty file that is used to indicate which disc this is for. The pack I have at hand has total of 3 discs, and the second disc include a file `DISC_02_03`

**SBZV_AAV.icf**

This file's purpose is still unknown, could be some special config for game on this system.

**SBZV_5.00.02_20150529201945_0_0_29234.app**

SBZV stands for Hatsune Miku Project Diva Arcade Future Tone, this matches with the code on my keychip, this could include the game image. `5.00.02` is probably the version number of the game. 
