---
title: Building an all-sky camera
subtitle: towards live aurora alerts for less than € 275
Description:
categories: [science, curiosity]
tags: []
readtime:
---

{% include img.html src="/images/2024/02/allsky-camera-overview.jpg" aspect_ratio=56.04 %}

1. The enclosure should be waterproof
2. The camera should be able to withstand temperatures down to -30C
3. There should be as few cables as possible to the camera
4. Maintenance should be as easy as possible


{% include img.html src="/images/2024/02/allsky-camera-3d-printed-mount.jpg" aspect_ratio=55.42 %}

{% include img.html src="/images/2024/02/allsky-camera-inside.jpg" aspect_ratio=61.88 %}

{% include img.html src="/images/2024/02/allsky-camera-assembly-screws.jpg" aspect_ratio=66.11 %}

{% include img.html src="/images/2024/02/allsky-camera-assembly-raspberry.jpg" aspect_ratio=53.51 %}

{% include img.html src="/images/2024/02/allsky-camera-assembly-sd-card.jpg" aspect_ratio=46.93 full_width=true %}

{% include img.html src="/images/2024/02/allsky-camera-assembled.jpg" aspect_ratio=63.50 full_width=true %}

{% include img.html src="/images/2024/02/allsky-camera-glue-clamp.jpg" aspect_ratio=44.17 %}

{% include img.html src="/images/2024/02/allsky-camera-dome-mounted.jpg" aspect_ratio=46.57 %}

{% include img.html src="/images/2024/02/allsky-camera-in-case.jpg" aspect_ratio=57.03 %}

{% include img.html src="/images/2024/02/allsky-camera-assembled-in-case.jpg" aspect_ratio=56.01 %}

{% include img.html src="/images/2024/02/aurora-detection-preview.png" aspect_ratio=96.23 %}

| Name                                                                                                                                                                                                                                                                       |    Cost | Qty. |        Total |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------:|-----:|-------------:|
| [Raspberry Pi 4 Model B - 4 GB](https://www.raspberrystore.nl/PrestaShop/en/raspberry-pi-v4/228-raspberry-pi-4b4gb-765756931182.html)                                                                                                                                      | € 47.95 |    1 |      € 47.95 |
| [Raspberry Pi 4 PoE+ HAT (2021)](https://www.raspberrystore.nl/PrestaShop/en/gpio-extension-boards/344-raspberry-pi-poe-hat-0633696492820.html)                                                                                                                            | € 24.95 |    1 |      € 24.95 |
| [HDMI - Micro HDMI cable 1.5m](https://www.raspberrystore.nl/PrestaShop/en/cables/235-hdmi-micro-hdmi-zwart-1m-5412810180363.html)                                                                                                                                         |  € 7.95 |    1 |       € 7.95 |
| [Raspberry Pi - HQ Camera M12 mount](https://www.raspberrystore.nl/PrestaShop/en/camera/476-raspberry-pi-hq-camera-5056561803227.html)                                                                                                                                     | € 60.95 |    1 |      € 60.95 |
| [M12 High Resolution Lens, 14ML, 184.6° Ultre wide angle lens](https://www.kiwi-electronics.com/en/raspberry-pi-boards-cases-addons-and-accessories-59/raspberry-pi-camera-accessories-133/m12-high-resolution-lens-14mp-184-6-ultra-wide-angle-2-72mm-focal-length-11267) | € 14.95 |    1 |      € 14.95 |
| [Brass M2.5 Standoffs 16mm - black plated - 2 pack](https://www.kiwi-electronics.com/en/brass-m2-5-standoffs-16mm-tall-black-plated-2-pack-1993)                                                                                                                           |  € 1.50 |    2 |       € 3.00 |
| [4 pin GPIO Header - 2x2 Female](https://www.kiwi-electronics.com/en/4-pin-gpio-header-2x2-female-3586)                                                                                                                                                                    |  € 2.50 |    1 |       € 2.50 |
| [40-pin GPIO Stacking Header - 2x20 Female](https://www.kiwi-electronics.com/en/40-pin-gpio-stacking-header-2x20-female-1640)                                                                                                                                              |  € 1.95 |    1 |       € 1.95 |
| [AXIS TM3813 Dome Clear](https://netcamcenter.com/en/products/other-products/tm3813-dome-clear)                                                                                                                                                                            | € 53.24 |    1 |      € 53.24 |
| [PVC Glue connector 75mm](https://www.gamma.nl/assortiment/martens-mof-pvc-grijs-2x-lijmverbinding-75x75-mm/p/B370108)                                                                                                                                                     |  € 2.49 |    1 |       € 2.49 |
| [PVC end cap with screw lid 75mm](https://www.gamma.nl/assortiment/martens-eindstop-grijs-met-schroefdeksel-75-mm/p/B019224)                                                                                                                                               |  € 3.49 |    1 |       € 3.49 |
| [PVC end cap 75mm](https://www.gamma.nl/assortiment/martens-eindkap-pvc-75-mm-grijs/p/B376208)                                                                                                                                                                             |  € 1.99 |    1 |       € 1.99 |
| [PVC closing cap 75mm](https://www.toolstation.nl/pvc-afsluitkap/p47591)                                                                                                                                                                                                   |  € 1.30 |    2 |       € 2.60 |
| [PVC glue](https://www.gamma.nl/assortiment/bison-hard-pvc-lijm-flacon-100ml/p/B427426)                                                                                                                                                                                    |  € 7.19 |    1 |       € 7.19 |
| [Ubiquiti Power over Ethernet injector 48Volt](https://www.dectdirect.nl/nl/ubiquiti-power-over-ethernet-injector-48-volt-giga.html)                                                                                                                                       | € 24.00 |    1 |      € 24.00 |
| [Waterproof Cable gland 3.0 - 6.5mm, Nylon](https://www.allekabels.nl/searchresult.php?keyword=1067045)                                                                                                                                                                    |  € 0.54 |    1 |       € 0.54 |
| [Bison Combi plastic glue](https://www.gamma.nl/assortiment/bison-2-componentenlijm-kombi-plastic-25-ml/p/B319597)                                                                                                                                                         | € 11.99 |    1 |      € 11.99 |
| PLA 3D printer filament (Price per kg)                                                                                                                                                                                                                                     | € 25.99 |  53g |       € 1.38 |
| Total                                                                                                                                                                                                                                                                      |         |      | **€ 273.11** |



Tools
- RJ45 Crimping tool
- Sanding paper
- 3d Printer
- Screwdriver