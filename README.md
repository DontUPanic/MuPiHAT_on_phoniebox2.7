# MupiHAT_on_phoniebox
This repository describes the setup of Phoniebox V2.7 with MuPiHAT on a RaspberryPi (Zero W) 

## ToDo

Fix Audio

# 1.  Install Phoniebox 2.7 Software

https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/INSTALL-COMPLETE-GUIDE#6-install-phoniebox-software

# 2. Fix Mopidy-Spotify

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

# 3. Setup the included OnOff-Shim

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
