# Plex-and-OMV
ursprüngliches YT Video
https://www.youtube.com/watch?v=yf0w7fu_KYw

Wichtig: Raspberry Pi OS Lite Bookworm 32 bit no desktop environment verwenden (zu finden unter legacy)

dann folgendes in dieser Reihenfolge ausführen:

1. SD Kart in Raspi stecken
2. 2. Raspi rödeln lassen
 

Problem: 2 HDDs sind dran und beim kopieren verliert OMBV ständig die Verbindung
Lösung: UA driver gegen alten USB-storage driver in der config austauschen 
original link: https://bashtan.ro/raspberry-pi-4-model-b-8gb-issue-hard-drives-disconnecting-software-solved/

copy paste von der Seite falls später mal nicht verfügbar:

The Problem:
After updating to Debian Bookworm, my external drives began disconnecting after some time or under heavy use. Initially, I suspected it was a power supply issue. However, I had been using the official Raspberry Pi power supply, and the issue didn’t exist before the update. After further research, I discovered the problem was related to the UAS driver, which doesn’t play well with ASMedia controllers.

The Solution:
To fix the problem, you need to disable the UAS driver and enable the older usb-storage driver for your device. Here’s how you can do it.

Step-by-Step Solution:
1. Check if UAS is active:
First, you need to verify if the UAS driver is enabled for your device. Open a terminal and run the following command:
```
lsusb -t
```
Look for a line that shows uas in the output. If you see it, then UAS is enabled for your device.
![image](https://github.com/user-attachments/assets/dc85f430-24f1-4351-9291-c5b9f2b40803)

2. Edit the Boot Configuration:
To disable the UAS driver, you will need to edit the boot configuration file. Open the file for editing with this command:
```
sudo nano /boot/cmdline.txt
```
This file contains the boot parameters. After the last word in the file (without any spaces), add the following:
```
usb-storage.quirks=174c:2362:u
```
It should look like this in the file:
```
console=tty1 root=PARTUUID=68cab3f9-02 rootfstype=ext4 fsck.repair=yes rootwait usb-storage.quirks=174c:2362:u
```
This command will disable the UAS driver and enable the older usb-storage driver for your external drive. The 174c:2362 is the device’s ID, and you need to replace it with the appropriate ID for your specific device.

3. Find Your Device ID:
To find the ID of your device, run the following command:
```
lsusb
```
This will display a list of connected USB devices. Find the line corresponding to your external drive, and note the ID (in the format 174c:2362 or something similar).

4. Reboot Your Raspberry Pi:
Once you’ve added the usb-storage.quirks line with the correct ID, save the file and reboot your Raspberry Pi:
```
sudo reboot
```
5. Verify the Changes:
After rebooting, check if the usb-storage driver is now active. Run the lsusb -t command again. If everything went correctly, you should now see that your external drive is using the usb-storage driver instead of UAS.

Conclusion:
That’s it! The issue should now be resolved, and your external drives should no longer disconnect after prolonged use or heavy activity. The fix disables the UAS driver and forces the system to use the more stable usb-storage driver, which works better with ASMedia controllersalso the speed was better now.
