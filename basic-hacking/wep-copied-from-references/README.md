# WEP (Copied from "References")

### Setup

{% code overflow="wrap" %}
```
sudo airmon-ng check kill

sudo airmon-ng start wlan0
```
{% endcode %}

### [Reconnaissance](../../#reconnaissance)

{% code overflow="wrap" %}
```
sudo airodump-ng wlan0mon
```
{% endcode %}

{% hint style="info" %}
Gather `CH` `ESSID` `BSSID` & Clients ; `AUTH WEP`
{% endhint %}

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>aireplay-ng WEP attack options</p></figcaption></figure>
