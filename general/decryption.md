# Decryption

### Wireshark

view (ribbon) > wireless toolbar (802.11 pref button) > Enable decryption > Decryption Keys edit

* WEP `aa:bb:cc:dd:ee` (optional :)
* WPA passphrase = `wpa-pwd pwd(:essid)` (omit essid to apply pwd to any last found essid in packet list)
* wpa-psk - pairwise master key (PMK) hex to decrypt WPA1/2 enterprise packet
  * when using PSK & SSID
  * when pwd contains a colon :
  * can only decrypt sessions after a full 4-way handshake (wireshark 256 association limit)
  * decrypted packets cannot be exported

<figure><img src="../.gitbook/assets/image (5).png" alt="wpa-psk hex generation"><figcaption><p>wpa-psk hex generation</p></figcaption></figure>

#### Check encryption

{% hint style="info" %}
type: beacon 8 / probe req 4 / probe res 5 / association 0 1&#x20;

ctl frames = irrelavant&#x20;

`!(wlan.fc.type == 1)`
{% endhint %}

frame details > IEEE802.11 Wireless Management > Fixed Parameters > Capabilities information > Privacy bit

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Privacy bit shows open network </p></figcaption></figure>

frame details > IEEE802.11 Wireless Management > Fixed Parameters > Capabilities information > Tagged parameters > Vendor Specific

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption><p>WPA IE in beacon (AKM = PSK), probe res would contain the same WPA IE (WPA1)</p></figcaption></figure>

WPA2 support would contain RSN info tag in beacon tagged param with AKM=PSK, and asso req would see client chosen what encryption mode

WPA3 support would contain AKM=SAE, 802.11w in RSN info, auth frames contain dragonfly handshake b4 4-way asso handshake, any disagreement during dragonfly in auth would finally reach grp 19

OWE: AKM = OWE, , with 802.11w & RSN, DHE pub key exchange in asso phrase

WPS: WPS tag, protocol EAP, WSC Done = finished WPS exchange, EAP failure = client can use credentials for 4-way handshake

802.11w PMF: RSN capabilities contains mgnt frame protection required/capable, Protected flag = 1 to ignore deauth attack from aireplay-ng, deauth packets could be a containment from a Wireless Intrusion Prevention System WIPS

