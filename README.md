# MuPiHAT on Phoniebox V2.7
This repository describes the setup of Phoniebox V2.7 with MuPiHAT on a RaspberryPi (Zero W) 

## ToDo

Fix Audio

# 1.  Install Phoniebox 2.7 Software

https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/INSTALL-COMPLETE-GUIDE#6-install-phoniebox-software

# 2. Fix Audio

1. Open config.txt via ```sudo nano /boot/config.txt``` and edit/add:
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
   

# 3. Fix Mopidy-Spotify

1. Download the latest gst-plugin-spotify_0.14.0.alpha.1-1_armhf.deb with ```cd ~ && wget https://github.com/kingosticks/gst-plugins-rs-build/releases/download/gst-plugin-spotify_0.14.0-alpha.1-1/gst-plugin-spotify_0.14.0.alpha.1-1_armhf.deb```.
2. Install the plugin. Run ```sudo dpkg -i gst-plugin-spotify_0.14.0.alpha.1-1_armhf.deb```.
3. Download and install latest Mopidy-Spotify: ```sudo python3 -m pip install --break-system-packages Mopidy-Spotify==5.0.0a3```.
4. If shell promts ```no such option: --break-system-packages``` pip is not up to date.
5. Therefore execute ```sudo python3 -m pip install --upgrade pip``` and try to install Mopidy-Spotify again as explained in 3.
6. Remove the plugin installer with ```rm gst-plugin-spotify_0.14.0.alpha.1-1_armhf.deb```
7. Next you have to put your credentials into ```nano /etc/mopidy/mopidy.conf```. Use https://mopidy.com/ext/spotify/#authentication to create the credentials.
8. Restart mopidy ```sudo systemctl restart mopidy```

compiled with the help of this thread:

https://github.com/mopidy/mopidy-spotify/discussions/405#discussioncomment-10881114

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
