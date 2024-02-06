# Basic Operations

### RDP

{% code overflow="wrap" %}
```

xfreerdp /d:sandbox(.local) /u:alex /p:'xxx' /v:10.5.5.20 +clipboard +drives /tls-seclevel:0
```
{% endcode %}

### Reconnaissance

Network interface info: `ip a`

Interface details: `sudo iw list`

{% hint style="info" %}
If it said "nl80211 not found." that mean we are using ieee80211 drivers. Else we are using mac80211 and the "iw list" output will print wireless card informations.
{% endhint %}

Interface's driver: `sudo airmon-ng`

USB ports status: `sudo lsusb -vv`

In-range APs: `sudo iw dev wlan0 scan | egrep "DS Parameter set|SSID:"`

### Setup

#### Create & bring up virtual interface in monitor mode

{% code overflow="wrap" %}
```bash

sudo iw dev wlan0 interface add wlan0mon type monitor

sudo ip link set wlan0mon up
```
{% endcode %}

#### Verify the setup

{% code overflow="wrap" %}
```bash

sudo iw dev wlan0mon info

sudo tcpdump -i wlan0mon
```
{% endcode %}

#### Remove VIF

{% code overflow="wrap" %}
```bash

sudo iw dev wlan0mon interface del

sudo airmon-ng stop wlan0mon
```
{% endcode %}

### Remote

{% hint style="info" %}
Pre-requisite:

* SSH to remote sys
* remote sys wireless interface in monitor mode
{% endhint %}

#### CMD

{% code overflow="wrap" %}
```bash

ssh root@10.11.0.196 "sudo -S tcpdump -U -w - -i wlan0mon" | sudo wireshark -k -i -
```
{% endcode %}

#### Wireshark

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption><p>Edit (ribbon) > preferences > advanced to reset default</p></figcaption></figure>
