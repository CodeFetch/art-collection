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


Analysis, conclusions and speculations
--------------------------------------

Based on the limited information obtained so far the result for now is:
* Most likely, that the calibration data contained in ART partition are not unique per router, but vary according to the revision of the board.
* The data are loaded directly without any CRC consistency facilitating the changes.
* The MAC address is not on the partition ART allowing test and distribute modified versions without bothering to adjust or be touting the MAC address.
* Various fields presented identical in the three ART images, some are relevant to the correct operation of wireless, others are obsolete or not used by the drive. I did not spend much time with them as it is not the focus.
* The fields 'Caps Tuning' (2.4GHz and 5GHz) are to the accuracy of the crystal clock. Different revisions of board normally will have these different parameters. A good indication that change the ART with another router without looking at the revision of the board may be bad idea.
* The tables 'Calibration' (2.4GHz and 5GHz) are to calibrate / compensate asymmetries between the chains and deviations from expected. Has significant variation between board revisions. Confirming that change the ART with another router without looking at the revision of the board is bad idea.
* The tables 'Target 2.4GHz (11B, 11G, HT20, HT40)', 'Target 5GHz (11A, HT20, HT40)' specify the maximum power due to the speed and fequency of the connection. For the analysis of the sources these tables should (and seem) have only the hardware power limitations, but can be used to impose a regulatory domain specific (I hope they are not used to this). These tables are vital to the health of your router and the wireless vicinity, because they limit the hardware to secure powers for the proper functioning. Change these values or even use a drive that ignores them has consequences. Power up the hardware limit can have a similar effect to a stereo at full volume, a lot of noise, a lot of distortion, it works worse than the more moderate powers, not to mention all that noise interferes in the wireless vicinity operation. Overheating generate thermal erosion deteriorates the power stage, further increasing noise and distortion and over time leads to burning of it. The power may be as high (remembering +3dB that is twice the power) the power stage burning occurs in a short time. Now assume that the hardware has protection from excess power, then none of the above is valid, all table values are merely informative, is useless exceed, are fakes values because the hardware limits the power with supposed to. And last, for much power to the router is a few meters and with little obstacle walls.
* The tables 'Regulatory (2.4GHz and 5GHz)' are to impose regulatory domain specific, limiting the maximum power, vary with where routers are marketed.


Credits
-------

* Many thanks to pepe2k for providing the awesome reverse engineering work done in https://github.com/pepe2k/ar9300_eeprom and the tool he provided
