# iGrill
Monitor your iGrill (mini, v2 or v3) or Pulse 2000 (with a Raspberry Pi 1/2/3) - and forward it to a mqtt-server

# NOTE
This is a new fork designed to be used with a new web app that I built.  I've broken compatibility with the
original versions.  For the web app, please see the igrillWeb project.  General changes:

* Added better bluetooth recovery, especially when the OS grabs the bluetooth connection before the monitor
* Fixed a bug where ctl-c would not stop the app if the app was successfully connected and monitoring
* Rebuilt the MQ sessions around using the HiveMQ public broker.  Now you don't need to worry about setting one up, but you still can if you'd like.'
* Changed output to a single JSON message instead of using seperate channels with a payload of a single int.
* Now using the bluetooth address in the topic so that each user will be guaranteed a unique topic name.

I'm interested in others trying this out paired with the web app so let me know how it works for you!'

## What do you need
### Hardware
* An iGrill Device (and at least one probe) - **iGrill mini**, **iGrill 2** or **iGrill 3** or a **Pulse2000**
* A Raspberry Pi

## Installation
1. clone this repo
1. install required modules using pip (see requirements.txt)
1. Create a dir for your config file(s) (E.g. ./config)
1. Add at least one device config (see ./exampleconfig/device.yaml) - You need the MAC address of your device, you can find it with `hcitool lescan`
1. start application `./monitor.py -c <path_to_config_dir` (or add -l debug)
1. enjoy

### systemd startup-script

Modify `examplescripts/igrill.service` to mach your setup and copy it to an appropriate place. E.g: `/lib/systemd/system/igrill.service`

Run `systemctl daemon-reload && systemctl enable igrill && systemctl start igrill`

Next time you reboot, the iGrill service will connect and reconnect if something goes wrong...

### Docker

1. Clone this repo
1. Install docker on your system.
1. Create a dir for your config file(s) (E.g. ./config)
1. Add at least one device config (see ./exampleconfig/device.yaml) - You need the MAC address of your device, you can find it with `hcitool lescan`
1. Build Docker image: `docker build . -t igrill`
1. Run docker image, mounting the config folder: `docker run --network host --name igrill -v <path_to_config_dir>:/usr/src/igrill/config igrill`
1. Profit!

## Troubleshooting

If your device is stuck on "Authenticating" the following has been reported to work:
1. within the file /etc/bluetooth/main.conf under [Policy] check the existence of
AutoEnable=true
1. Comment out below line in /lib/udev/rules.d/90-pi-bluetooth.rules
by prefixing "#" the line ACTION=="add", SUBSYSTEM=="bluetooth", KERNEL=="hci[0-9]*", RUN+="/bin/hciconfig %k up"

If you are struggling with flaky Bluetooth connection. (E.g. The device connects and works for a while, then disappears)
Try to test without using onboard Bluetooth and WiFi at the same time. Either with a cabled Ethernet connection or with a separate WiFi or Bluetooth dongle.
