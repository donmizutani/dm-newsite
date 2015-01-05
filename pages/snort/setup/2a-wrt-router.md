---
layout: page
title: Setting up Snort - Part 2a - DD-WRT and OpenWrt Supported Routers
meta-description: home router, dd-wrt, openwrt, buffalo whr-600d, unbrick, brick
---
[< Back: Part 2 - Mirroring Network Traffic](/pages/snort/setup/2-mirroring-network-traffic)


You will need a router with DD-WRT or OpenWrt customer router firmware installed.

- [http://www.dd-wrt.com](http://www.dd-wrt.com)
- [https://openwrt.org](https://openwrt.org)

### Supported Routers

Check if your router is supported. Note that not all home routers are supported, due to the processor, ram and nvram that your router comes with. 

DD-WRT

- [http://www.dd-wrt.com/wiki/index.php/Supported_Devices](http://www.dd-wrt.com/wiki/index.php/Supported_Devices)

OpenWrt

- [http://wiki.openwrt.org/toh/start](http://wiki.openwrt.org/toh/start)

If it is not in the list above, try searching the forums. You may be in luck, as I was with the Buffalo WHR-600D I purchased.

- [http://www.dd-wrt.com/phpBB2](http://www.dd-wrt.com/phpBB2)
- [https://forum.openwrt.org](https://forum.openwrt.org)

### Install the firmware

Once you have a supported router, it is time to install the firmware.

**However, this website will not show you how to do this.** Go to the forum and wiki sites for the respective firmware - they have extremely detailed guides on how to prepare, install and recover from a brick - yes, I bricked my WHR-600D before I got it working :)

If you brick yours as well, first thing is not panic. These routers are still alive, albeit in zombie-like state. Visit the forums and see how to re-flash the firmware to unbrick your router.

**Note that the rest of Part 2 will be based on OpenWrt. Depending on your DD-WRT firmware version, it may work. In the case of the WHR-600D, this was not supported.**

## Personal Experience

If your router is not supported, you will have to go out and purchase one that is. 

I was in the unsupported router group, so I went out to buy a Buffalo WHR-600D, which costed 4000 yen (~$40 USD). A cheap solution well worth what I got out of it. 

Make sure it is the **exact** same model name. An extra letter or higher revision number that is not listed means it is not work.

**Do not try to flash an older or similar naming firmware. It will brick your router!**

This is especially true if the model just came out. You will need to look for one that is at least a year old, if not more.

If you live in Japan, as I do, Buffalo brand home routers are your best bet. Head to your nearest Bic Camera, Yodobashi Camera, or another favorite store and pick one up.

... TODO ...

Reference
How to unbrick the Buffalo WHR-600D

[< Back: Part 2 - Mirroring Network Traffic](/pages/snort/setup/2-mirroring-network-traffic)

