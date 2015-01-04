RaspberryPVR
============

Notes about creating a Freeview PVR from a Raspberry Pi, a cheap DVB-T dongle and RaspBMC software.

Hardware
--------

-   Raspberry Pi model B (original version)

-   8GB SD card

-   August DVB-T205 USB Freeview Tuner Stick  
    Picked a cheap one that uses the Realtek RTL2832 chipset:  
    £10 from Amazon: http://www.amazon.co.uk/gp/product/B002EHVP9C

-   HDMI cable, ethernet cable (positioned near router for a direct connection but Wifi could be used).

Software
--------

-  RaspBMC http://www.raspbmc.com/  
   A minimal Debian Linux distribution with based on Kodi (XBMC) v14 Media Center software

-  TvHeadEnd https://tvheadend.org  
   A backend for streaming MPEG from DVT-B tuner. This is ready installed in the RaspBMC distribution.

Steps
-----

Install RaspBMC on SD card. Followed the Mac/Linux instructions here:

> http://www.raspbmc.com/wiki/user/os-x-linux-installation/

Connected up the Raspberry Pi:

-   Power from USB powerbrick (the USB port on the TV would't supply enough power but that might just be my setup).
-   Ethernet cable to router
-   HDMI cable to TV
-   DVB-T stick in USB port
-   SD card created above

Powered up RPi

RaspBMC installs (takes 10 minutes)

Kodi user interface appears.

> Interesting note: I can control Kodi through my television's (a Panasonic Viera) remote control. The RPi doesn't have an IR receiver so I can only imagine this is coming through the HDMI cable. This is a very useful feature which I hadn't expected. 

Change default password

  From a terminal on a computer attached to the same network:

>  ssh pi@IP_ADDRESS

  The IP address will be displayed in the SYSTEM -> System Info menu in Kodi

  The default password is 'raspberry'. Log in and change the password:

>  passwd pi

Enabled TVHeadEnd add-on in Kodi

>  SYSTEM -> Settings -> Add-ons -> Disabled Add-ons -> Tvheadend HTSP Client -> Enabled [select]

>  SYSTEM -> Settings -> Live TV -> General -> Enabled [select]

Reboot the RPi using the Kodi restart button.

  At this point, tvheadend should have been started in the /etc/init script but Kodi could not connect to it. Logging in confirms this - tvheadend is not running. 
  
  Looking at /etc/init/tvheadend.conf:

>   cat /etc/init/tvheadend.conf
>   \#\# TVHeadend server
>   
>   description "tvh"
>   author "none"
>   
>   start on (started xbmc and enable-tvheadend)
>   stop on (disable-tvheadend or runlevel [!2345])
>   
>   respawn
>   
>   pre-start script
>   test -x /usr/bin/tvheadend || { stop; logger -t tvheadend "cannot execute /usr/bin/tvheadend, exiting" ; exit 1; }
>   modprobe dvb_hdhomerun
>   sleep 5
>   sudo userhdhomerun -f
>   sleep 5
>   end script
>   
>   exec su - $(grep 1000 /etc/passwd | cut -f1 -d":") -c "/usr/bin/tvheadend -C -d"

  This looks like it should start tvheadend if xbmc is started and has enable-tveadend. Not sure why it doesn't start but will investigate this later. In the meantime start tvheadend manually:

>nohup tvheadend -C -d &

Setup tvheadend

  Tvheadend is configurable from a browser at http://IP_ADDRESS:9981

Purchase and install MPEG-2 license key

  Purchased from Raspberry Pi store http://www.raspberrypi.com
  
  Delivered by email. The license can be installed onto the SD card in the 'config.txt' as described in the license email or can be done in the RaspBMC configuration interface. This is hidden in Apps->RaspBMC Settings. 

Operation
---------

TV and Radio are now available as main Kodi options. Electronic program guide works and recording/playback. Need to install external storage for recording (currently recording to home directory on SD card). 


