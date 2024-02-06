---
description: wpa_supplicant -i wan0 -c <config file>
---

# wpa\_supplicant connection

{% hint style="info" %}
Use `sudo dhclient wlan0` to request IP after connection.
{% endhint %}

### wpa\_supplicant.conf

```
# WPA-MGT (WPA Enterprise, PEAP)
network={
  ssid="NetworkName"
  scan_ssid=1
  key_mgmt=WPA-EAP
  identity="Domain\username"
  password="password"
  eap=PEAP
  phase1="peaplabel=0"
  phase2="auth=MSCHAPV2"
}
```

```
# WPA-PSK (WPA1/2 Personal)
network={
  ssid="network"
  scan_ssid=1
  psk="password"
  key_mgmt=WPA-PSK
}
```

```
# WEP
network={
  ssid="network"
  key_mgmt=NONE
  wep_key0="12345"  # 5 or 13 characters, or a hexkey starting with 0x
  #wep_key0=0102030405 #hex
  wep_tx_keyidx=0
}
```
