---
layout: post
published: true
title: Openshift lab windows 10 pro laptop
date: 2019-09-23 01:28
category: openshift
author:  "datahawklab"
tags: [openshift,lab,codeready containers]
summary: what i did to buy a very capable laptop and upgrade it to a very powerful laptop with SSD and 16GB dof DDR3 RAM.
---


Here is what i did to buy a very capable laptop and upgrade it to a very powerful laptop with SSD and 16GB dof DDR3 RAM.

**Note that this will not age well, so if you're looking at using this, I would only consider this for 2019, beyond that this probably will not be relevant.**

***warning: The instructions I provide are very specific and are provided as-is. I bought a new laptop, the upgraded as noted below***
***if you use instructions below, you do so at your own risk***
***DO NOT attemt this on own personal laptop with existing data***
**These intstructions are SPECIFICALLY for upgrading new Aspire E5-576-392H laptop**

What do you get with this?

1. **You get a very fast SSD drive laptop with 16GB of dual channel RAM for $630 including tax and shipping**

- assuming you have Amazon Prime.

2. you get an easy to do upgrade as Acer makes this particular laptop easy to upgrade.

3. The slow 1TB drive that comes with you lapto is reused as a storage and archive area.

4. you get bootup in less than 10 seconds.

5. you can run minishift/Codeready containers on this laptop.

6. You're installing from the Windows 10 Disk without any
bloatware.

7. You get a USB WIndows 10 pro install disk (with license).

These are the following items i purchased, all from Amazon with free shipping.
prices below include tax.

| item | price |
|:--------|:-------:|--------:|
| laptop | $353.83 |
| windows 10 pro upgrade | $107.79 |
| SSD| $79.19 |
| USB blash drive | $7.90 |
| 16GN DDR3 RAM | $95.50 |
| Total | $644 |

laptop description:
Acer Aspire E 15, 15.6" Full HD, 8th Gen Intel Core i3-8130U, 6GB RAM Memory, 1TB HDD, 8X DVD, E5-576-392H
amazon link:
[Acer Aspire E5-576-392H]

SSD description:
WD Blue 3D NAND 500GB PC SSD - SATA III 6 Gb/s, M.2 2280 - WDS500G2B0B
amazon link:
[WD WDS500G2B0B]

USB Flash Drive Description:
Kingston Digital 8GB Data Traveler 3.0 USB Flash Drive - Yellow (DTIG4/8GB )
amazon link:
[USB Flash Drive]

RAM Description:
Samsung ram memory 16GB kit (2 x 8GB) DDR3 PC3L-12800,1600MHz, 204 PIN SODIMM for laptops
amazon link:
[Samsung RAM]

---
layout:     post
title:      "openshift lab"
subtitle:   " \"ansible ingo\""
date:       2021-05-28 12:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - deploy
    - openshift
---


1. On the new laptop, first upgrade to Windows 10 Pro
  * you can do this by clicking the Windows Store icon. All you should have to do is pay and then wait a few minutes for the upgrade to complete.
2. put the flash drive into one of the USB slots then create a Windows 10n bootable USB
  * Go to [Windows 10 media creation tool]
  * click Download tool no, run the tool once its downloaded
  * **shutdown and unplug the laptop**
3. Install physical memory and SSD
  * **make sure the laptop is off and unplugged**
  * open the three on the back of the laptop,and pry open the cover.
  * Take out the two existing memory sticks and put in the new ones.
  * Open the screw, and attach the SSD, make sure to screw in the SSD drive tightly
  * slide out the existing hard drive slightly but dont remvoe it
4. Close the cover, but dont screw the cover back in
5. restart the laptop with the USB left in, go through the screens and intall Windows 10 from the USB onto the SSD.
6. **Turn off the laptop and unplug**
7. open the cover again, and push the hard drive that you pushed out back in.
9. close the cover, screw in the 3 screws you removed.
10. start the laptop and hit F2 to get to the BIOS

  * use the right arrow to get to the Boot menu
  * use the down arrow to select the SSD (WDS500G..) hit F6 until that drive is the first one.
  * then save and exit
11. Log in to windows, search for 'This PC', then right click --> Properties on the 'D' drive and make sure its the 1TB drive your laptop came with
  * Then right click again on the 'D' --> format. **make sure sure that youre formatting the old 1TB drive your laptop came with**
12. Let your laptop go through all the updates.

[Acer Aspire E5-576-392H]:https://www.amazon.com/gp/product/B079TGL2BZ/ref=ppx_yo_dt_b_asin_title_o01__o00_s00?ie=UTF8&psc=1
[WD WDS500G2B0B]:https://www.amazon.com/gp/product/B073SBX6TY/ref=ppx_yo_dt_b_asin_title_o00__o00_s01?ie=UTF8&psc=1
[USB Flash Drive]:https://www.amazon.com/gp/product/B00G9WHMHC/ref=ppx_od_dt_b_asin_title_o00_s00?ie=UTF8&psc=1
[Samsung RAM]:https://www.amazon.com/gp/product/B00KEAEX54/ref=ppx_od_dt_b_asin_title_o00_s01?ie=UTF8&psc=1
[Windows 10 media creation tool]:https://www.microsoft.com/en-us/software-download/windows10
