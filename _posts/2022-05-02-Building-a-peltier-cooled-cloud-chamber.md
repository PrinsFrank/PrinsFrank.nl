---
title: Building a peltier cooled cloud chamber
subtitle: Making radiation visible
Description:
categories: [science, curiosity]
tags: []
readtime:
---

As some of you may know, I have been building a cloud chamber for a few months now. And while I might add some improvements like permanent temperature readings and graceful shutdown in the feature, this weekend I finished the initial build. And I'd like to share it with you here!

{% include img.html src="/images/2022/05/cloud-chamber-fully-assembled.jpg" aspect_ratio=62.2 %}

I have been excited about cloud chambers since I was a child. But while they are easy to build using dry ice in the states, that dry ice is not easy to come by in the Netherlands. A couple of years ago I learned about the ability to cool to very low temperatures using peltier elements, and since then the idea to build my own has been hunting me.

## Preparation

The first step was figuring out what size cooling plate I was going to be building. I've researched quite a lot, and most other projects out there used a square plate the exact size of a peltier element. I wanted a more wide plate, so decided I was going to place two elements next to each other. That decision limited the kind of water cooling elements I could be using, so I ended up with the Cooler master H45, which was also quite cheap. As I was going to use two elements I also needed two of these coolers. 

{% include img.html src="/images/2022/05/cloud-chamber-parts.jpg" aspect_ratio=51.4 %}

For those of you that are not familiar with peltier elements, they are basically a heat pump, where heat is transferred from one side of the element to the other. There are different kind of Peltier elements available, with different heat throughputs. For this build I initially chose 12710 elements, but after some experimentation with stacking different kind of elements I ended up with a 12712 stacked on a 12715. You can see the initial batch of 12710 elements above, and now that I knew the basic components and their power consumption I could also order a power supply. As I was planning on using aluminium profiles in an open case I chose for a modular power supply so that the cable management was more clean.

## Cold plate; 3D modeling and printing

Now that the initial components have arrived, it was time to design the main component construction: the cold plate. The water cooling blocks have a nice octagonal design where we can [3D print a part for that makes a perfect pressure fit](https://www.thingiverse.com/thing:5030459){:target="_blank" rel="noreferrer noopener"}. As we want to apply a lot of pressure to make the heat transfer from the copper plate to the cooling block more efficient, I decided on designing around 6 spacer bolts that can be tightened from both sides. An extra [3d printed plate](https://www.thingiverse.com/thing:5030459){:target="_blank" rel="noreferrer noopener"} is designed to be pressure fit over these bolts from the top, and both these segments are mounted to an aluminium profile below the middle of the cooling block using bolts. [An extra section](https://www.thingiverse.com/thing:5030459){:target="_blank" rel="noreferrer noopener"} is added on top of that to keep the peltier elements from sliding around through the cooling paste. These have as little contact with the elements themselves te prevent any unnecessary heat transfer.

{% include img.html src="/images/2022/05/cloud-chamber-cooling-assembly.jpg" aspect_ratio=87.6 %}

After this initial batch of 3D design - which was a nice opportunity for me te learn Fusion 360 - and iterative prints, I now had a design that was ready for the next step. Now that we had definitive dimensions it was now time to order a custom cut copper plate! As I only had experience with thin sheets of copper I was worried that it would flex too much. But copper is also the most heat conductive, which makes it perfect. So I decided to go with the thickest size copper I could order in these small dimensions, which was 4mm. Upon arrival though, it was obvious that copper was much more sturdy than I expected, especially when I had to drill through it for the 6 mounting points. So if you want to follow this build on your own, you can probably use a thinner sheet of copper, which will also be easier to work with.

## Frame assembly

As this thing was going to be too big to print frame parts for, I decided on finally using aluminium profiles. A set of 12 profiles of half a meter only costs around €30! After first assembling the heat sinks of the water coolers to 90 degree brackets, the first dimensions got clear. Leaving space for the power supply and using the base plate for the cooling blocks as guides, the frame started to take shape. You will also notice that I test fitted the cooling elements here without actually mounting them. And a PCB was soldered with connectors for all the 3- and 4-pin connectors required for the cooling elements' fans and water pumps.

{% include img.html src="/images/2022/05/cloud-chamber-initial-frame-assembly.jpg" aspect_ratio=53.5 %}

## Cold plate assembly

After stacking the peltier elements with a fair amount of cooling paste and finally the copper plate, the cooling plate assembly was ready. Or so I thought;

{% include img.html src="/images/2022/05/cloud-chamber-cooling-plate-assembly.jpg" aspect_ratio=44.4 %}

What followed was multiple weekends struggling to get the temperature low enough. I was aiming for -35C, which I read online as a benchmark from other people who built similar devices. But whatever I did, I couldn't get past the -20C or -25C threshold. 

{% include img.html src="/images/2022/05/temperature-sensor.jpg" aspect_ratio=76 %}

Until I found out what the problem was: up until this moment I had used four exactly the same elements, the top ones running in series effectively making them run at 50% power, the bottom ones at a 100%:

| Stack 1       | Stack 2       |
|---------------|---------------|
| Copper plate  | Copper plate  |
| 12710 - 50%   | 12710 - 50%   |
| 12710 - 100%  | 12710 - 100%  |
| Cooling block | Cooling block |

But when measuring the temperature between those elements, it was still above zero. When changing to new elements with different capacity this issue was now solved and with a bit of insulation I now could reach -35C!;

| Stack 1       | Stack 2       |
|---------------|---------------|
| Copper plate  | Copper plate  |
| 12712 - 50%   | 12712 - 50%   |
| 12715 - 100%  | 12715 - 100%  |
| Cooling block | Cooling block |

## Power and more power

As these peltier elements use a lot of power, I decided against hot wiring the power supply. These PC power supplies are usually switched on using a switch on the motherboard of a PC, so are not automatically turned on when connected. There is an option to connect two pins in, but I decided on buying a GeeekPi ATX breakout board.

As I didn't want to strip the nice modular connectors of the power supply, and I also hadn't found female PCIE connectors, I decided it would be a good idea to connect the Peltier elements to the breakout board. So on the same night I reached the target temperatures, the board de-soldered its own connectors from too high power consumption;

{% include img.html src="/images/2022/05/self-destructing-power-board.jpg" aspect_ratio=60.6 %}

I bought a new breakout board, and decided it was time to cut the connectors. The two 12712 in series and each of the 12715s now got their own PCIE cable connecting them to their own 12v output on the power supply. Another output was used for the cooling element fans and pumps.

## Chamber assembly

The reason this project took so long as it did was because I couldn't find the perfect chamber. I had bought two bell jars previously, the first one being too small and the second one being way too big. The small one couldn't be used for obvious reasons, but the big bell jar was so high that there was a lot of turbulence in the jar making it practically unusable. 

But during a short visit to Ikea I stumbled upon a glass oven-proof storage container that was the perfect size! I could finally finish this beast of a project!

As the chamber base was too large to fit on my 3D printer, I had to design it into [4 separately printable parts, 2 being the mirror image of the other 2](https://www.thingiverse.com/thing:5030459){:target="_blank" rel="noreferrer noopener"}. A slight downwards curve was added from the base plate to allow for any excess ethanol to be collected in that area without obstructing the view. A separate slot for a rubber isolation strip was also added, and 4 holes on each side were added as mounting points. You can see these four prints being assembled onto the aluminium profiles below;

{% include img.html src="/images/2022/05/cloud-chamber-base-plate-assembly.jpg" aspect_ratio=63.4 %}

And mounted on top of the cloud chamber, the plate fit perfectly around the base plate!

{% include img.html src="/images/2022/05/cloud-chamber-initial-assembly-top.jpg" aspect_ratio=29.3 %}

It is quite visible that the copper started to oxidize from all the handling and mounts for the temperature sensor. But to increase contrast between the white 'clouds' and the device itself, A black window foil was applied over it, which also sealed all the seams between the copper plates and the PLA prints, making it now waterproof. A rubber window seal was put into the specifically designed groove.

{% include img.html src="/images/2022/05/cloud-chamber-top-section.jpg" aspect_ratio=57.3 %}

## Final touches

Only a few things are missing. A light is needed at a very shallow angle shining from the back. After soldering 8 LEDs to two PCBs a [3D printed part](https://www.thingiverse.com/thing:5030459){:target="_blank" rel="noreferrer noopener"} could be designed to keep the PCBs at the perfect angle. After some cable management and a new rubber seal for better insulation, it was now time to test everything together!

After checking the conductivity of the window foil and getting ice crystals from air humidity, it was clear that everything was ready.

{% include img.html src="/images/2022/05/cloud-chamber-ice-formation.jpg" aspect_ratio=58.2 %}

## Running the cloud chamber

After all this it is now finally time to run this thing! As you can see below, there are still some issues. Even though I switched from the very though rubber window seal to a very soft door seal, there were still very small disturbances in air flow inside, so for now it has to run with some diving weights stacked on top to firmly press the glass container on the seal. As some plastics are vulnerable to alcohol, two 80mm fan covers are connected to each other to make a stand for the ethanol. The ethanol is soaked into toilet paper, and the stack of toilet paper is rested on the stand. As ethanol has a very low boiling point, it eventually fills the entire chamber. Any ethanol that is close to the cold plate is highly susceptible to disturbances, so when any radiation hits it a cloud is formed of ethanol turning into a liquid again and raining down.

{% include img.html src="/images/2022/05/cloud-chamber-running.jpg" aspect_ratio=69.8 %}

A few small flakes of a radioactive mineral are placed in the center of the chamber. Even though my camera has trouble focussing because of the fogginess, you can still see the streaks of alpha, beta and gamma radiation quite nicely! 

<video width="100%" height="100%" controls preload="auto" >
  <source src="/images/2022/05/cloud-chamber-with-radioactive-mineral.mp4" type="video/mp4">
</video>

## Parts and building your own

All the 3D models for printing are available on [my thingiverse profile](https://www.thingiverse.com/thing:5030459){:target="_blank" rel="noreferrer noopener"}

<details markdown="1">
<summary>A full list of parts can be found below, for if you want to build a cloud chamber yourself;</summary>

| Component                                                                                                                                                                                                           | Price   |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| [Cooler master v605 (modular, 650W)](https://tweakers.net/pricewatch/1602666/cooler-master-v650-gold-v2.html){:target="_blank" rel="noreferrer noopener"}                                                           | € 99,00 |
| [Corsair Hydro Series H45](https://tweakers.net/pricewatch/542693/corsair-hydro-series-h45.html){:target="_blank" rel="noreferrer noopener"} 2x                                                                     | €144,00 |
| [Copper base plate cut to size 4mm, 14mm x 75mm](https://plaatopmaatwinkel.nl/product/koper-plaat/){:target="_blank" rel="noreferrer noopener"} x 2                                                                 | € 32,91 |
| [Aluminium profile 20x20 B-type slot 6 - 12 x 500 mm](https://www.motedis.nl/shop/Slot-profiles/Profile-20x20-B-type-slot-6::999991.html){:target="_blank" rel="noreferrer noopener"}                               | € 27,05 |
| [Peltier TEC1 12715 15A](https://www.vanallesenmeer.nl/Peltier-koelelement-TEC1-12715-40x40x3.4mm-15.4V-15A){:target="_blank" rel="noreferrer noopener"} 2x                                                         | € 17,90 |
| [Peltier TEC1 12712 12A](https://www.vanallesenmeer.nl/Peltier-koelelement-TEC1-12712-40x40x3.5mm-15.4V-12A){:target="_blank" rel="noreferrer noopener"} 2x                                                         | € 17,50 |
| [Steel connection angle x 10](https://www.motedis.nl/shop/Slot-profile-accessories/Bracket-for-Aluminium-profiles/Steel-Connection-Angle-galvanised-20::999999014.html){:target="_blank" rel="noreferrer noopener"} | €  8,78 |
| [Bracket 20x20 B-type slot 6 x 10](https://www.motedis.nl/shop/Slot-profile-accessories/Bracket-for-Aluminium-profiles/Bracket-20x20-B-type-slot-6::999991062.html){:target="_blank" rel="noreferrer noopener"} x 6 | € 20,61 |
| [Cover cap 20 B-type slot 6 x 200](https://www.motedis.nl/shop/Slot-profile-accessories/Covers/Cover-Cap-20-B-Type-slot-6::99999557.html){:target="_blank" rel="noreferrer noopener"}                               | € 24,20 |
| [GeeekPi 24/20 pin ATX breakout board](https://www.amazon.nl/gp/product/B08MBYYX66/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1){:target="_blank" rel="noreferrer noopener"} x 2                                | € 25,98 |
| Various M4 hex bolts (8mm, 10mm, 30mm, 40mm)                                                                                                                                                                        | € 30,00 |
| Various electrical: PCB, Solder, wiring, connectors, LEDs                                                                                                                                                           | € 20,00 |
| Fan cover 80mm, 2 times                                                                                                                                                                                             | €  2,00 |
| [Glass ovenproof storage pot](https://www.ikea.com/nl/nl/p/ikea-365-voorraadpot-rechthoekig-glas-70359203/){:target="_blank" rel="noreferrer noopener"}                                                             | €  4,49 |
| [Arctic Silver 5, 12 grams](https://tweakers.net/pricewatch/135673/arctic-silver-5-12-gram.html){:target="_blank" rel="noreferrer noopener"} x 2                                                                    | € 29,80 |
|                                                                                                                                                                                                                     | €504,22 |

</details>
