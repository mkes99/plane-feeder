# Install FlightAware, PlaneFinder and FlightRadar24 on a raspberry pi already running dump1090
## Instal Flightaware

Update current raspberry pi

```
sudo apt update
sudo apt full-upgrade
sudo reboot
```
Download and install the FlightAware APT repository package, which tells your Pi's package manager (apt) how to find FlightAware's software packages in addition to the packages provided by Raspbian.

```
wget https://www.flightaware.com/adsb/piaware/files/packages/pool/piaware/f/flightaware-apt-repository/flightaware-apt-repository_1.2_all.deb
sudo dpkg -i flightaware-apt-repository_1.2_all.deb
```
Update your apt package sources and install PiAware. This will install all the required dependencies on your Raspberry Pi.
```
sudo apt update
sudo apt install piaware
```
Enable automatic and manual (web-based, via your request) PiAware software updates. These updates are disabled by default. To leave updates disabled, skip this step.
```
sudo piaware-config allow-auto-updates yes
sudo piaware-config allow-manual-updates yes
```
Install dump-fa
```
sudo apt install dump1090-fa
```
```
sudo reboot
```
You should be able to vist <ip>8080 and see the dump1090-fa web interface. If you are using a PiAware device, you can also visit <ip>8080 to see the dump1090-fa web interface.

FlightAware Claim your new device
You should wait about four or five minutes for your PiAware to start and then you can associate your FlightAware account with your PiAware device to receive all the benefits.

Once your device is running, please:
Look up the IP address in your router admin and go to the assigned IP address in a browser on the same network. If the device hasn't been claimed a link to claim the PiAware device will display.

Or

Claim your PiAware client on FlightAware.com
If after 5 minutes your device hasn't displayed as claimed try restarting the device, if that still doesn't work re-confirm the Wi-Fi settings (if using Wi-Fi) are correct. Lastly contact us at ADSBsupport@FlightAware.com. Check your stats page (link below) to confirm it was claimed.

## Edit dump1090-fa config to brodcast beast
````
sudo nano /etc/default/dump1090-fa
````
add this line of code
````
RECEIVER_OPTIONS="--net --net-only --net-beast --net-ro-size 1000 --net-ro-interval 1 --net-ri-port 30005"
````
## Install PlaneFinder Client
````
wget http://client.planefinder.net/pfclient_x.x.x_armhf.deb
````
(replace x.x.x with the current version number – you can get the latest version from here
https://planefinder.net/sharing/client) download the correct version and note the version number to change the above command

Once installed you will need to enter your PlaneFinder account details and select the correct device type. If you are using a PiAware device, you can also visit <ip>::30053 to see the PlaneFinder web interface. Enter your email address, lat and long and click next on the following screen select `beast` and enter the ip for your device and port 30005 and save. You should now be collecting data. 

## Install FlightRadar24
````
wget -qO- https://fr24.com/install.sh | sudo bash -s
````
Go through the confinguration process your reciever type will be "avr-tcp" and your host will be <ip>:30002. If you need to you can edit your configuration file at /etc/fr24feed.ini. Also make sure in your config you have your path set `path="/usr/lib/fr24/dump1090"`. You can also visit <ip>::8754 to see the FlightRadar24 web interface.

## useful commands and links
````
sudo systemctl status <client>
sudo systemctl restart <client>
sudo systemctl stop <client>

Client calls :
planefinder - pfclient
flight radar - fr24feed
Flightaware - piaware

https://github.com/abcd567a/piaware-ubuntu-debian-amd64/blob/master/README.md
https://planefinder.net/coverage/client
https://www.flightaware.com/adsb/piaware/build
````