# Setting up a Raspberry Pi 3 as a Wireless Access Point

### What you will need:
* Raspberry Pi 3 (with onboard wifi)
* Ethernet cable and accessible port (if you would like to be able to use the internet)
* **It is recommended that you have heat sinks applied to your Raspberry Pi as the CPU will get hot over long periods of use**

### Setup:
First you should update your Raspberry Pi. This can be done by running the following commands:
```
sudo apt-get update
sudo apt-get upgrade
sudo rpi-update
```

Then you can install the required modules with the command:
```
sudo apt-get install dnsmasq hostapd
```
Edit the **/etc/dhcpcd.conf** file by using the command:
```
sudo nano /etc/dhcpcd.conf
```
Add the following lines to the **/etc/dhcpcd.conf** file:
```
interface wlan0
static ip_address=192.168.4.1/24
```

Next we will edit the **/etc/dnsmasq.conf** file by entering the command:
```
sudo nano /etc/dnsmasq.conf
```
Add the following lines to the **/etc/dnsmasq.conf** file:
```
interface=wlan0
dhcp-range=192.168.4.8,192.168.4.250,255.255.255.0,12h
```



Now we will edit the **/etc/hostapd/hostapd.conf** by entering the command:
```
sudo nano /etc/hostapd/hostapd.conf
```
Add the following lines to the **/etc/hostapd/hostapd.conf** file:
```
interface=wlan0
driver=nl80211
ssid=YourWifiName
hw_mode=g
channel=6
wmm_enabled=0
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=YourWifiPassword
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```
* You can replace the ssid and wpa_passphrase with whatever you would like to make the wifi name and password. The password must be 8 characters or more in length in order to work.
* Ignore_broadcast_ssid can be changed from 0 to 1 to prevent the network from being broadcasted. This way you will be required to know the ssid to connect to the network.
* The channel number may vary depending on your situation. The best channel to use is the one with the least amount of other wifi networks using it. Refer to [this guide](https://docs.google.com/document/d/1lRwgjD9J2gTaqkj3PUsFFV98LAx1LIREFeMcXqNHJUE/edit?usp=sharing) for more information on how to find the best wifi channel.

Next we will need to edit the **/etc/default/hostapd** file by entering the command:
```
sudo nano /etc/default/hostapd
```
Find and edit the following line in the **/etc/default/hostapd** file to match this:
```
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```
**Make sure you uncomment this line by deleting the # before the line**

Next edit the file **/etc/sysctl.conf** with the command:
```
sudo nano /etc/sysctl.conf
```

Find the following line in the **/etc/sysctl.conf** file and uncomment it:
```
net.ipv4.ip_forward=1
```
The network should be up and running now. To enable internet access follow these next steps.

You will need to make iptable rules by entering the command:
```
sudo iptables -t nat -A  POSTROUTING -o eth0 -j MASQUERADE
```
Now you will make these rules permanent by entering the command:
```
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
```
Next, to ensure this starts up every time, enter the following command:
```
sudo nano /etc/rc.local
```
In the **/etc/rc.local file**, add the following line above the exit 0 code.
```
iptables-restore < /etc/iptables.ipv4.nat
```
Now you can restart the Raspberry Pi with the command:
```
sudo reboot
```
The network will now be set up with access to the internet.


### Troubleshooting:
You may need to unmask the hostapd service in order to run it. This can be done with the following command:
```
sudo systemctl unmask hostapd
```
To check the status of hostapd and dnsmasq use the following commands:
```
sudo systemctl status hostapd.service
sudo systemctl status dnsmasq.service
```


The following command can also be used to check the status of your network:
```
sudo journalctl -xe
```
This is also a helpful command to check for errors.

You can check the syntax of the code in your dnsmasq file by using the following command:
```
sudo dnsmasq --test
```
**DO NOT modify the /etc/network/interfaces file**






### Helpful Links

[1]https://www.raspberrypi.org/forums/viewtopic.php?t=193228

[2]https://www.howtogeek.com/197268/how-to-find-the-best-wi-fi-channel-for-your-router-on-any-operating-system/

### Contact Information
Email: vachanpatel2@gmail.com
