# Protection
The Nu's OS has many protection system in place to keep us out. I have identified a few while working with the system.

* Keyboard Filter (this is basic for embedded systems, ran as automatic service, can be easily disabled)
* Enhanced Write Filter ([link](https://www.exorint.com/faq/2017/06/20/how-to-configure-enhanced-write-filter-ewf-under-windows-os), this prevent any file changes on critical drive, make sure have it disabled if you want to mess with the file system when the system is booted) 
* sgpreboot.exe bitlocker check (the start up will halt at STEP00_04 if drives were decrypted, it will run reinitialize and format the critical drive and reboot loop)

Things to keep in mind:

* Its always easier if you can manipulate the filesytem off-device, trying inside the system is a hassle
* Once you have recovery key for the bitlocker drives, don't try to run the system with drives decrypted, or you run into the issue I am trying to dig myself out of.
* If you have to mess with the system when system is booted, turn off keyboard filter and do your change and then turn off EWF.
* Back up encrypted drive image in case it tries to self destruct.


## To diable EWF

```
ewfmgr -all -commitanddisable // this command will also commit all the changes stored in ram.
```

(suggestion: do not turn off EWF, use `-all -commit` to apply changes to hard disk when you need it, it will save you from a lot of trouble if you mess up the filesystem)