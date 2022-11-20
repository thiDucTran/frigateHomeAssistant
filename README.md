# 1. Overview
## 1.1 Why
The need to have a [NVR](https://www.samsara.com/guides/nvr-camera-system/) solution with some sort of automation/alerting feature; there is also the desire to do it with as much [FOSS](https://itsfoss.com/what-is-foss/) software as possible. Please do not expect this document to be in-depth and all-knowing; I am writing this mainly as a self-reminder/quick-start document for when I need it.
## 1.2 Requirements
Fulfilled and TODO requirements for the chosen solution
* [x] DIY/FOSS
* [x] NVR
* [x] Local and remote dashboard
* [x] Motion detection
* [x] Remote notifications and local alerting
* [x] Resilent (highly available or at least acceptable from my point of view)
# 2. Hardware
Good read: `Frigate`'s [recommended hardware](https://docs.frigate.video/hardware) link. Personally, [`PoE`](https://www.samsara.com/guides/poe-security-camera/) adoption for any hardware that you choose is really a time-saver.

Ultimately, this section would help you decide on what to buy and how much the cost would be.

FINALLY, please note that my setup **only has 1 camera**; so beef things up if necessary for your case.
## 2.1 Cameras
As recommended from the link above, I went with the [`Loryta(Dahua) T5442TM-AS-LED`](https://amzn.to/3uFLtxB)
## 2.2 Power
* [x] `PoE` to power camera
* [ ] Uninterruptible power supply [(`UPS`)](https://en.wikipedia.org/wiki/Uninterruptible_power_supply)
  * for me, this is nice to have, but might be required for your case.
## 2.3 Networking
* [x] `PoE` network switch
* [x] `Cat5e` or any ethernet cable that can be used with `PoE`
* [x] No wireless. Nothing should be wireless (except for how you access the system remotely)

*NOTE*: In terms of costs, I am disregarding labor cost and misc costs related to hardware installations (drills, screws, mishap devastations, etc ...); this is because every house is different. Please plan carefully surrounding camera locations, power outlets, and etc... Please also do research on, `What is the maximum distance for power and data when using PoE?` and how it applies to your setup.
## Server
* [x] Something to host [`Frigate`](https://frigate.video/)
* [x] Something to host [`Home Assistant`](https://www.home-assistant.io/)
* [x] Something to host [`Mopidy`](https://mopidy.com/)

For `Mopidy`, I am using a [Raspberry Pi 4 1GB](https://www.canakit.com/raspberry-pi-4.html) + a cheap **USB speaker** (externally powered - meaning not powered via USB of the Raspberry Pi 4. Something like [this](https://www.amazon.com/Logitech-3-5mm-Compact-Laptop-Speakers/dp/B003CP0OT2)). The rest, I am using an old laptop (specs below) plus a **1TB external USB** 3.0 harddrive for storage.
```bash
            .-/+oossssoo+/-.               ubuntuuser@ubuntuuser-HP-Pavilion-g6-Notebook-PC
        `:+ssssssssssssssssss+:`           ------------------------------------------------
      -+ssssssssssssssssssyyssss+-         OS: Ubuntu 22.04.1 LTS x86_64
    .ossssssssssssssssssdMMMNysssso.       Host: HP Pavilion g6 Notebook PC
   /ssssssssssshdmmNNmmyNMMMMhssssss/      Kernel: 6.0.0-9.1-liquorix-amd64
  +ssssssssshmydMMMMMMMNddddyssssssss+     Uptime: ***
 /sssssssshNMMMyhhyyyyhmNMMMNhssssssss/    Packages: ***
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Shell: bash 5.1.16
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   Resolution: 1366x768
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   Terminal: /dev/pts/1
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   CPU: Intel i3-2350M (4) @ 2.300GHz
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   GPU: Intel 2nd Generation Core Processor Family
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Memory: 7901MiB
 /sssssssshNMMMyhhyyyyhdNMMMNhssssssss/
  +sssssssssdmydMMMMMMMMddddyssssssss+
   /ssssssssssshdmNNNNmyNMMMMhssssss/
    .ossssssssssssssssssdMMMNysssso.
      -+sssssssssssssssssyyyssss+-
        `:+ssssssssssssssssss+:`
            .-/+oossssoo+/-.
```
* [ ] `Google Coral TPU` - As encouraged from [here](https://docs.frigate.video/hardware/#google-coral-tpu), feel free to try this out. But my guide will not cover on how to setup and use this. My setup is running without this and I am very happy with the result; meaning, I do not see the need, and it is too expensive for me at the moment (caused by supply shortage).
# 3. Software
Before proceeding, it is assumed that all of our cameras, switches, and servers are powered and networked.
## 3.1 MQTT Broker
Follow the instruction to install [Mosquitto](https://mosquitto.org/download/) broker. Feel free to use any other broker of your choice. Below is my config for Mosquitto
```bash
# /etc/mosquitto/mosquitto.conf
listener 1883 0.0.0.0
persistence true
persistence_location /var/lib/mosquitto/
allow_anonymous true
log_dest file /var/log/mosquitto/mosquitto.log
```
## 3.2 Frigate
The official installation steps are [here](https://docs.frigate.video/installation).
### 3.2.1 Prepare the config file
My `config.yaml` file is as below
```yaml
mqtt:
  # in my local host file, my-mqtt resolves to 127.0.0.1
  host: my-mqtt
  port: 1883

birdseye:
  enabled: False
  width: 1280
  height: 720
  quality: 8
  mode: motion
cameras:
  camera_1: # <------ Name the camera
    ffmpeg:
      hwaccel_args: -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 -hwaccel_output_format yuv420p
      inputs:
        - path: rtsp://username:passwordHere@192.168.1.108:554/cam/realmonitor?channel=1&subtype=00 # <----- Update for your camera. Try to google or your camera's documentation to know what the correct stream URL is.
          roles:
            - detect
            - rtmp
            - record
detect:
  enabled: True
  width: 1280 # <---- update for your camera's resolution
  height: 720 # <---- update for your camera's resolution
  fps: 5
  max_disappeared: 25 # 5x fps
  stationary:
    interval: 5
    threshold: 50 # 10x interval
    max_frames:
      default: 3000
      objects:
        person: 1000
record: # <----- Enable recording
  enabled: True
  retain:
    days: 2
    mode: motion
  events:
    retain:
      default: 10
      mode: active_objects

motion:
  threshold: 30 # The higher the less sensitive. 25 is default
  contour_area: 30 # From low to high sensitivity: 50 30 15
  improve_contrast: False
```
### 3.2.2 Install
I installed Frigate via `Docker` and starts it with `docker compose --file /home/ubuntuuser/Downloads/frigate-compose.yaml up --detach` where `frigate-compose.yaml` is as below
```yaml
version: "3.9"
services:
  frigate:
    network_mode: host
    container_name: frigate
    privileged: true # this may not be necessary for all setups
    restart: unless-stopped
    image: blakeblackshear/frigate:0.11.1
    shm_size: "64mb" # update for your cameras based on calculation above
    devices:
      # - /dev/bus/usb:/dev/bus/usb # passes the USB Coral, needs to be modified for other versions
      # - /dev/apex_0:/dev/apex_0 # passes a PCIe Coral, follow driver instructions here https://coral.ai/docs/m2/get-started/#2a-on-linux
      - /dev/dri/renderD128 # for intel hwaccel, needs to be updated for your hardware
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /home/ubuntuuser/Downloads/cameraStuff/config.yml:/config/config.yml:ro
      - /home/ubuntuuser/Downloads/cameraStuff/frigate:/media/frigate
      - type: tmpfs # Optional: 1GB of memory, reduces SSD/SD Card wear
        target: /tmp/cache
        tmpfs:
          size: 1500000000
    ports:
      - "5000:5000"
      - "1935:1935" # RTMP feeds
```
## 3.3 Home Assistant
For my setup to work, I found out the hard way that the Home Assistant flavor `MUST` support `Add-ons`. So, from [here](https://www.home-assistant.io/installation/#compare-installation-methods), we only have 2 installation methods of: `OS` or `Supervised`

I went with sthe [`Supervised`](https://www.home-assistant.io/installation/linux) option