---
title: 'Setting up IPV6 for Biznet Home on the EG8245H5 router'
date: 2021-10-02T15:21:49+0000
draft: false
Desciption: 'Setting up IPV6 for Biznet Home on the EG8245H5 router'
---

For some reason, it's not configured by default.

![Config screenshot](https://i.imgur.com/Xhrc1GB.png)

1. Login to your router Web UI on `192.168.18.1`. The default credentials are `Adminep:epAdmin`
2. Go to ⚙️ > Maintenance Diagnosis > Configuration file & Download your current config, just in case.
3. Go to ⚙️ > WAN, Open your current config & take a screenshot of it
4. Delete your current configuration, this will cut off your internet connection
5. Create a new connection, copying your previous config, with these changes:
  - Set protocol type to "IPv4/IPv6"
  - Set password to your dial password. It's included in the documents when you've setting it up for the first time. A copy is also emailed to you.
  - Set everything in the IPv6 Information block to DHCPv6
6. Your internet should be back on now. Disconnect & reconnect your devices to get them an IPv6 address.
7. Check your IPv6 status at https://ipv6test.google.com/

If things are broken, restore settings from the config screenshot, or restore the config file at the same place you created the backup from.
