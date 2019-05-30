# linkpi_smart_wifi

A Node application which makes connecting your RaspberryPi to your home wifi easier

## Install

```sh
$git clone https://github.com/sabhiram/raspberry-wifi-conf.git
$cd raspberry-wifi-conf
$npm update
$bower install
$sudo npm run-script provision
$sudo npm start
```


```sh
$sudo cp assets/init.d/raspberry-wifi-conf /etc/init.d/raspberry-wifi-conf 
$sudo chmod +x /etc/init.d/raspberry-wifi-conf  
$sudo update-rc.d raspberry-wifi-conf defaults
```

#### `hostapd`

```
$cd raspberry-wifi-conf
$sudo mv /usr/sbin/hostapd /usr/sbin/hostapd.OLD
$sudo mv assets/bin/hostapd.rtl871xdrv /usr/sbin/hostapd
$sudo chmod 755 /usr/sbin/hostapd
```

#### `dhcpcd` 

## Usage


1. Check to see if we are connected to a wifi AP
2. If connected to a wifi, do nothing -> exit
3. (if not wifi, then) Convert RPI to act as an AP (with a configurable SSID)
4. Host a lightweight HTTP server which allows for the user to connect and configure the RPIs wifi connection. The interfaces exposed are RESTy so other applications can similarly implement their own UIs around the data returned.
5. Once the RPI is successfully configured, reset it to act as a wifi device (not AP anymore), and setup it's wifi network based on what the user selected.
6. At this stage, the RPI is named, and has a valid wifi connection which it is now bound to.

Typically, I have the following line in my `/etc/rc.local` file:
```
cd /home/pi/raspberry-wifi-conf
sudo /usr/bin/node server.js
```

Note that this is run in a blocking fashion, in that this script will have to exit before we can proceed with others defined in `rc.local`. This way I can guarantee that other services which might rely on wifi will have said connection before being run. If this is not the case for you, and you just want this to run (if needed) in the background, then you can do:

```
cd /home/pi/raspberry-wifi-conf
sudo /usr/bin/node server.js < /dev/null &
```

## User Interface

In my config file, I have set up the static ip for my PI when in AP mode to `192.168.44.1` and the AP's broadcast SSID to `rpi-config-ap`. These are images captured from my osx dev box.

Step 1: Power on Pi which runs this app on startup (assume it is not configured for a wifi connection). Once it boots up, you will see `rpi-config-ap` among the wifi connections.  The password is configured in config.json.

<img src="https://raw.githubusercontent.com/sabhiram/public-images/master/raspberry-wifi-conf/wifi_options.png" width="200px" height="160px" />

Step 2: Join the above network, and navigate to the static IP and port we set in config.json (`http://192.168.44.1:88`), you will see:

<img src="https://raw.githubusercontent.com/sabhiram/public-images/master/raspberry-wifi-conf/ui.png" width="404px" height="222px" />

Step 3: Select your home (or whatever) network, punch in the wifi passcode if any, and click `Submit`. You are done! Your Pi is now on your home wifi!!

## Testing

