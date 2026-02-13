# Plex-and-OMV
## Neuer initialversuch gemäß Anleitung hier
https://pimylifeup.com/raspberry-pi-plex-server/

### ^ zusätzlich dazu noch   
`sudo wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash` 
dann weiter in OMV im Browser mit heise video

## ursprüngliches YT Video
https://www.youtube.com/watch?v=yf0w7fu_KYw

## DEPRECATED 

## Problem 1: Plex ist nur erreichbar wenn PC angeschalten

## Lösung 1: Pfade verweisen auf korrekten Ordner auf HDD aber falsch
Fall für meine config:

1. SSH into Raspi
2. run ``` "df -h" ``` to see all connected drives
3. look for HDDs such as ``` "/srv/dev-disk-by-uuid-3694b700-02bd-46ea-999e-ffad60d35121" ```
4. run ``` "cd /srv/dev-disk-by-uuid-3694b700-02bd-46ea-999e-ffad60d35121" ```
5. Gucken das die korrekten Ordner wie "Filme", Serien etc da sind
6. Diese Pfade in die Plex Library kopieren
``` /srv/dev-disk-by-uuid-3694b700-02bd-46ea-999e-ffad60d35121/18TB/Serien ```
``` /srv/dev-disk-by-uuid-d5442fef-12d7-4e35-bef9-abd57d68c68d/Filme ```

## Problem 2: Ich will meine Plex Metadaten kopieren / sichern

## Lösung 1: Pfade sind hier:
Raspberry Pi Plex dir:
``` /var/lib/plexmediaserver/Library/Application Support/Plex Media Server ```

Windows Plex dir: 
``` %LOCALAPPDATA%\Plex Media Server\ ```

WinSCP muss für den Datenaustausch Raspberry Pi <> Windows auf dem Win PC installiert werden
donwload hier https://winscp.net/eng/download.php

Nun stoppen wir den plexdienst auf dem Raspberry
``` sudo systemctl disable plexmediaserver.service ```

Nun habe ich die relevante Datei, ```  com.plexapp.plugins.library.db ``` von Windows kopiert und per WinSCP auf den Raspberry Pi geschoben, nach 
``` /var/lib/plexmediaserver/Library/Application Support/Plex Media Server/Plug-in Support/Databases ``` 

- Ich muss die Daten als root user verschieben, da ich als "Pi" user die vorhandene DB nicht überschreiben konnte.
- Root user erstellen auf dem raspi: ```sudo passwd root```Passwort ausdenken und eingeben
- Dann mit WinSCP auf dem Raspi einloggen, DB files verschieben
- Dann in WinSCP den Owner auf User "Plex" umstellen, berechtigung auf 777 stellen
- Nun neustarten ``` sudo reboot ```
- Nun Service wieder enablen & starten
``` sudo systemctl enable plexmediaserver.service ```
``` sudo systemctl start plexmediaserver.service ```


## Problem 3: 2 HDDs sind dran und beim kopieren verliert OMV ständig die Verbindung
Lösung: UA driver gegen alten USB-storage driver in der config austauschen 
original link: https://bashtan.ro/raspberry-pi-4-model-b-8gb-issue-hard-drives-disconnecting-software-solved/

copy paste von der Seite falls später mal nicht verfügbar:

## The Problem 4:
After updating to Debian Bookworm, my external drives began disconnecting after some time or under heavy use. Initially, I suspected it was a power supply issue. However, I had been using the official Raspberry Pi power supply, and the issue didn’t exist before the update. After further research, I discovered the problem was related to the UAS driver, which doesn’t play well with ASMedia controllers.

## The Solution:
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

if 2 HDDs are connected, two things must be added, see
![image](https://github.com/user-attachments/assets/cef7a10c-7120-496f-b182-242f27ad4793)


This command will disable the UAS driver and enable the older usb-storage driver for your external drive. The 174c:2362 is the device’s ID, and you need to replace it with the appropriate ID for your specific device.

3. Find Your Device ID:
To find the ID of your device, run the following command:
```
lsusb
```
This will display a list of connected USB devices. Find the line corresponding to your external drive, and note the ID (in the format 174c:2362 or something similar).
![image](https://github.com/user-attachments/assets/f5481780-942d-409b-95b3-fbbd91526342)

4. Reboot Your Raspberry Pi:
Once you’ve added the usb-storage.quirks line with the correct ID, save the file and reboot your Raspberry Pi:
```
sudo reboot
```
5. Verify the Changes:
After rebooting, check if the usb-storage driver is now active. Run the lsusb -t command again. If everything went correctly, you should now see that your external drive is using the **usb-storage** driver instead of UAS. (zumindest einer)

![image](https://github.com/user-attachments/assets/08e0c1af-157a-41ba-b358-d5735270cbcc)

Conclusion:
That’s it! The issue should now be resolved, and your external drives should no longer disconnect after prolonged use or heavy activity. The fix disables the UAS driver and forces the system to use the more stable usb-storage driver, which works better with ASMedia controllersalso the speed was better now.

## Problem 5: nach OMV install wird der raspi trotz Kabel nicht im Netz gefunden  
Lösung:  auf raspi einloggen
`sudo omv-firstaid`
dann fix network interface, ja auf alles, rödel rödel und es geht wieder


## Problem 6   ich muss die HDD von Plex an einen win PC anschließen
Lösung: BTFRS Treiber auf Windows installieren, siehe hier
`https://github.com/maharmstone/btrfs`

