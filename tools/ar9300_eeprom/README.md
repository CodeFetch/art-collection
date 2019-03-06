**This is a hard fork of pepe2k's [ar9300_eeprom](https://github.com/pepe2k/ar9300_eeprom) tool to analyze ART dumps**

Tool for Atheros ART partition
==============================

Dump, remove regulatory limits and fixes the Atheros ART partition with ar9300 layout, compatible with WDR3600, WDR4300, WDR4310 and more.

* *I have to thank some (many) people who provided tips and information on forums that saved weeks of hard work.*


Development status
------------------

* Dump of the ART partition with ar9300 layout that enables the analysis of settings.
* Have removes the regulatory domain specific (puts all the power in 60dB as suggested by source), leaving it to the drive and configuration apply the appropriate regulatory domain.
* Fix the table 'Calibration 5GHz' that is poorly formatted / incomplete (worked correct only between channels 149 and 165) in WDR4310 v1.0 board 2050500271 r1.3. The values are interpolated, but until I have in hand a WDR4300 ART partition with the same board revision is that I can do.


Compiling
---------

* Compile for Linux is only run the command 'make' the project root directory.
* The source codes are prepared to be compiled in Visual Studio.


How everything started
----------------------

I purchased a router WDR4310 (Chinese version of WDR4300) when installed DD-WRT was detected that the 5GHz worked correct only between channels 149 (5745MHz) and 165 (5825MHz). I tried the OpenWrt and problem persisted, with search the web the conclusion was that the ART partition imposed some restriction, but I not found a solution.

The other day I was looking for how to overclock into WDR4310, I found a [great article](http://aspiregemstone.blogspot.com.br/2014/12/overclocking-tp-link-wdr4300.html) that made everything very simple and yet clarified several points, making it much easier in trying to solve the problem of 5GHz restriction.

* In WDR4300 the MAC address is not on ART partition but in the upper u-boot partition (64KB U-Boot + 64KB settings).
* The modified U-Boot allows you to update the ART partition easily and safely.
* ART partition has the calibration of the wireless then change the partition WDR4310 by the WDR4300 should remove the restrictions.
* With the [backup of WDR4300](https://github.com/gwlim/Openwrt_Firmware/tree/master/TP-Link_TL-WDR3500-3600-43XX-WM4350R/TL-WDR4300-BackUp_Image_Only) closed everything needed to test.

The test was successful, the WDR4310 worked properly in 5GHz. But I thought the solution was too simplistic, because the ART partition contain the calibration of the wireless so even though all work good, suspected that the calibration of WDR4310 should be close to WDR4300 but not equal. I decided the one looking in the source code of OpenWrt, everything is there and only find the correct sources, was lucky to find them.


Looking the source code of OpenWrt
----------------------------------

The starting point was 'linux-(version)/arch/mips/ath79/mach-tl-wdr4300.c' that is for routers WDR3600, WDR4300 and WDR4310 that has the same hardware (WDR3600 has the same board but the components were suppressed a chain). Analyzing this source has confirmation that the data is in 0x1000 (the offset in ART partition) to 2,4GHz and the data is in 0x5000 to 5GHz and the MAC address that is in u-boot partition.

The Atheros wireless drive for this router is in 'linux-(version)/drivers/net/wireless/ath/ath9k'. Analyzing the sources, I found that the ART partition data is loaded directly into the struct ar9300_eeprom (definition in the source 'ar9003_eeprom.h'). The starting point for a dump were to ath9k_hw_ar9003_dump_eeprom and ar9003_dump_modal_eeprom functions in source 'ar9003_eeprom.c'. After a long analysis field-by-field, mainly in the source 'ar9003_eeprom.c', have had the first dump version.


License
-------

This project is Free Software, licensed under version 2 of the **GNU General Public License**.


Credits
-------

* [pepe2k](https://github.com/pepe2k) contribution with ART partitions.
