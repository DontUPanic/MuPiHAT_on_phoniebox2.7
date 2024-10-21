# MuPiHAT on Phoniebox V2.7
This repository describes the setup of Phoniebox V2.7 with MuPiHAT on a RaspberryPi (Zero W) 


# 1.  Install Phoniebox 2.7 Software

Follow Chapter 6 of this guide line by line!

https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/INSTALL-COMPLETE-GUIDE#6-install-phoniebox-software

The other chapters are worth reading as well

Find the extract below:
>## 6. Install Phoniebox software
>
>If you want to install the **Spotify+ version**, [read this first](https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/Spotify-FAQ).
>
>Run the following command in your SSH terminal and follow the instructions
>
>```bash
>cd; rm install-jukebox.sh; wget https://raw.githubusercontent.com/MiczFlor/RPi-Jukebox-RFID/master/scripts/installscripts/install-jukebox.sh; chmod +x install-jukebox.sh; ./install->jukebox.sh
>```
>
>1. `Yes` to `Continue interactive installation`
>1. `No` to the `Wifi Setting step` - it's already set!
>1. `No` to "Use Headphone as iFace?" in `CONFIGURE AUDIO INTERFACE (iFace)`- use `Speaker` 
>1. Setup Spotify (optional)
>    1. You need to generate your personal Spotify client ID and secret
>    1. Visit the [Mopidy Spotify Authentication Page](https://mopidy.com/ext/spotify/#authentication)
>    1. Click the button `Authenticate Mopidy with Spotify`
>    1. Login to Spotify with your credentials
>    1. Once logged in, the code snippet on the website is updated with your `client_id` and `client_secret`
>    1. Provide your Spotify `username`, `password` and paste your `client_id` and `client_secret` into your terminal
>1. `Yes` to `CONFIGURE MPD`
>1. `Yes` to `FOLDER CONTAINING AUDIO FILES`
>1. Optional: In this scenario, we do not install GPIO buttons, so feel free to choose `No`
>1. `Yes` to `Do you want to start the installation?`
>1. ... Wait a bit for the installation to happen ...
>1. `Yes` to `Have you connected your RFID reader?`
>1. `1` to select `1. USB-Reader`
>1. Choose the `#` that resonates with your RFID reader, in our case `HXGCoLtd Keyboard`
>1. `Yes` to `Would you like to reboot now?`
>
>---
>
>## 7. Verify Phoniebox setup
>
>1. Open a browser in your computer and navigate to your Raspberry Pi: `http://raspberrypi.local`
>1. You should see the Phoniebox UI
>1. In your navigation, choose `Card ID`
>1. Swipe one card near your RFID reader. If `Last used Chip ID` is automatically updated (you might hear a beep) and shows a number, your reader works
>1. Verify Spotify (optional)
>    1. Click `Spotify+` in the menu
>    1. Mopidy opens, a second web player which was also installed
>    1. You should be able to search and play Spotify content here
>


# 2. Fix Audio

1. Open config.txt via ```sudo nano /boot/config.txt``` and edit/add [^1]:
   ```
   #MuPiHat audio config:  
   dtparam=i2c_arm=on  
   dtparam=i2c1=on 
   dtoverlay=max98357a,sdmode-pin=16  
   #  
   #dtparam=spi=on  
   # Enable Onboard audio output: 
   dtparam=audio=on
   ```
3. ```sudo reboot```
4. Check if your audio works: ```aplay /usr/share/sounds/alsa/Front_Center.wav```
5. If it works, skip to the next chapter - If NOT, do the following steps:
6. Check ```sudo raspi-config``` -> 1   System Options ->  S2   Audio -> select the device "MAX98357A".
   
   The number before the device is the device no. Mine says "0   MAX98357A"

7. Edit ```sudo nano /etc/asound.conf```so the device number matches the device number in your raspi-config:
   ```
   pcm.!default {
        type asym
        playback.pcm {
                type plug
                slave.pcm "output"
        }
        capture.pcm {
                type plug
                slave.pcm "input"
        }
   }

   pcm.output {
        type hw
        card 0        ### put your device number here!
   }

   ctl.!default {
        type hw
        card 0        ### put your device number here!
   }
   ```
8. ```sudo reboot```
9. Check audio again: ```aplay /usr/share/sounds/alsa/Front_Center.wav```

[^1]: https://mupihat.de/pages/faq


# 3. Fix Mopidy-Spotify [^2]

1. Download the latest gst-plugin-spotify_0.14.0.alpha.1-1_armhf.deb with ```cd ~ && wget https://github.com/kingosticks/gst-plugins-rs-build/releases/download/gst-plugin-spotify_0.14.0-alpha.1-1/gst-plugin-spotify_0.14.0.alpha.1-1_armhf.deb```.
2. Install the plugin. Run ```sudo dpkg -i gst-plugin-spotify_0.14.0.alpha.1-1_armhf.deb```.
3. Download and install latest Mopidy-Spotify: ```sudo python3 -m pip install --break-system-packages Mopidy-Spotify==5.0.0a3```.
4. If shell promts ```no such option: --break-system-packages``` pip is not up to date.
5. Therefore execute ```sudo python3 -m pip install --upgrade pip``` and try to install Mopidy-Spotify again as explained in 3.
6. Remove the plugin installer with ```rm gst-plugin-spotify_0.14.0.alpha.1-1_armhf.deb```
7. Next you have to put your credentials into ```nano /etc/mopidy/mopidy.conf```. Use https://mopidy.com/ext/spotify/#authentication to create the credentials.
8. Restart mopidy ```sudo systemctl restart mopidy```

[^2]: https://github.com/mopidy/mopidy-spotify/discussions/405#discussioncomment-10881114

# 4. Setup the included OnOff-Shim

1. Install the device driver ```sudo curl https://get.pimoroni.com/onoffshim | bash```
2. Change the config ```sudo nano /etc/cleanshutd.conf```
   to
   
   ```
   # Config for cleanshutd
   # Commented out values will be reverted to defaults,
   # and may not work on any given board.
   
   # OnOff SHIM uses trigger 17 and poweroff 4
   # Zero Lipo uses trigger 4 and poweroff off
   # pHAT BEAT uses trigger 12 and powerof off
   # Default values are trigger 4 and poweroff off

   #daemon_active=1
   trigger_pin=17
   led_pin=13
   poweroff_pin=4
   hold_time=2
   shutdown_delay=0
   polling_rate=1
   ```
3. ```sudo reboot```
