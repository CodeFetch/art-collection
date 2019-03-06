Collection of Atheros Radio Test dumps
======================================

Introduction
------------

The ART partition is a region on flash chips of routers with a Qualcomm Atheros WLAN chipset. It contains calibration data which is e.g. being used by the ath9k driver. 
The purpose of this repository is to compare dumps from different router models from all over the world to reverse engineer the fields.
The `tools` folder contains software for analyzing the dumps.

We need your help!
------------------

If you own a router with Qualcomm Atheros chipset running OpenWrt, please take a dump of your ART parititon and make a pull-request if one of the following points applies:

* No dump for your model, yet
* Different region/country
* Different version on the label
* Different version printed on the mainboard
* Different MD5SUM

How to contribute
-----------------

* Take a dump of your ART partition using OpenWrt
```
		(This command will show you all MTD partitions)
		cat /proc/mtd


		dev:    size   erasesize  name
		mtd0: 00020000 00010000 "u-boot"
		mtd1: 000f1808 00010000 "kernel"
		mtd2: 006de7f8 00010000 "rootfs"
		mtd3: 002e0000 00010000 "rootfs_data"
		mtd4: 00010000 00010000 "art"
		mtd5: 007d0000 00010000 "firmware"


		(To backup ART partition in this example, run)
		cat /dev/mtd4 > /tmp/art.bin
```

* Make a pull-request contributing the ART partition dump. Send your ART partition with the following information:

  * Model and version found in the router label

	<p align=center><img src="https://static.tp-link.com/image003_1482220537501u.png" alt="Router label" width="487" height="161"><br>source: tp-link.com</p>

  * Board number and revision normally found together in one of the board corners

  * The country where you bought the router

  * Report if you know any abnormal behavior of the wireless router

* Your contribution as a developer is welcome. The ath9k driver uses other layouts beyond the ar9300, interpreting them increases the number of compatible routers.

* Help improving this documentation

Credits
-------

* Many thanks to pepe2k for providing the awesome reverse engineering work done in https://github.com/pepe2k/ar9300_eeprom and the tool he provided
