# 1. Overview
## 1.1 Why
The need to have a [NVR](https://www.samsara.com/guides/nvr-camera-system/) solution with some sort of automation/alerting feature; there is also the desire to do it with as much [FOSS](https://itsfoss.com/what-is-foss/) software as possible.
## 1.2 Requirements
Fulfilled and TODO requirements for the chosen solution
* [x] DIY/FOSS
* [x] NVR
* [x] Local and remote dashboard
* [x] Motion detection
* [x] Remote notifications and local alerting
* [x] Resilent (ighly available or at least acceptable from my point of view)
# 2. Hardware
Good read: `Frigate`'s [recommended hardware](https://docs.frigate.video/hardware) link. Personally, [`PoE`](https://www.samsara.com/guides/poe-security-camera/) adoption for any hardware that you choose is really a time-saver.

Ultimately, this section would help you decide on what to buy and how much the cost would be.
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

*NOTE*: In terms of costs, I am disregarding labor cost and misc costs related to hardware installations (drills, screws, mishap devestations, etc ...); this is because every house is different. Please plan carefully surrounding camera locations, power outlets, and etc... Please also do research on, `What is the maximum distance for power and data when using PoE?` and how it applies to your setup.
## Server
* [x] Something to host [`Frigate`](https://frigate.video/)
* [x] Something to host [`Home Assistant`](https://www.home-assistant.io/)
* [x] Something to host [`Mopidy`](https://mopidy.com/)

From the above, I am using a [Raspberry Pi 4 1GB](https://www.canakit.com/raspberry-pi-4.html) + a cheap **USB speaker** (externally powered - meaning not powered via USB of the Raspberry Pi 4. Something like [this](https://www.amazon.com/Logitech-3-5mm-Compact-Laptop-Speakers/dp/B003CP0OT2)). The rest, I am using an old laptop (specs below) plus a **1TB external USB** 3.0 harddrive for storage.
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
## 3.1 Installs
### 3.1.1 Frigate
### 3.1.2 Home Assistant
## 3.2 Configs
### 3.2.1 Frigate
### 3.2.1 Home Assistant