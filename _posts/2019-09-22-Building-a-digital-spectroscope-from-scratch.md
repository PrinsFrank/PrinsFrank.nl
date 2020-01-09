---
title: Building a digital spectroscope from scratch.
subtitle: From webcam to 3d model and fabrication.
Description:
categories: [science]
tags: []
readtime: 8
---

I've wanted to use a spectroscope, and my aim has been to make it part of my home lab for a while. But with my budget split between travel and other instruments like a 3d-printer, microscope and oscilloscope, I decided to build my own. I'm certainly not the first one to, as there are a variety of how-to's and even kits available online. But with most of these made from cardboard or likewise materials and either not digital or with very low resolution I wanted a more permanent and high resolution solution.

Over a year ago I bought the cheapest 3d-printer available online: the Anet a8. Apart fom being a fire hazard and as so not to be left alone, it's a really useful tool, but until now only used to print replacement parts for things like a glove compartment and a fridge, and some custom brackets for various things.

I decided to change that. After some research I came to the conclusion that I had to design my own 3d-model as none were available. The main component of the build will be a webcam, and as it's the only part that I can't really customize I had to decide on what webcam I would use before continuing.

Four things were important in my decision. Firstly, price. It should also be a popular model or form factor so other people can use the 3d-model as well without modification. The horizontal resolution should be as high as possible (vertical resolution doesn't really matter), but most important of all: Sensitivity for the IR spectrum and easy IR filter removal. With easy disassembling and small form factor being bonus points. I decided on the Logitech C920 Pro, with a reasonable price around €60.

I also early on decided I wanted a so called 'variable slit' setup where the light intake is adjustable. I decided on razor blades as the thin but sturdy item that was also standardized in size.

# Required components

To start this build for yourself you need the following items:
- Diffraction grating foil of a 1000 lines/<sub>mm</sub>
- 2 old-school metal razor blades
- Logitech C920 (Pro) or similar webcam
- 4 bolts <sub>M3, 40mm (This case is not designed in freedom units)</sub>
- The [3D-printed parts](https://www.thingiverse.com/thing:3872984)
- Some poster tack

# Webcam disassembly

With all the screws hidden behind rubber stickers and corners I was very happy to see an [ifixit guide for the exact model webcam I had](https://www.ifixit.com/Guide/Logitech+C920+Webcam+Disassembly/115077). Make sure the webcam is fully operational before you start! After following steps one through ten the only thing left to do is cut the plastic case with some cutting pliers so the circuit board and cable are free on their own. Make sure to keep all the screws that you get during disassemly as we need them to assemble the spectroscope itself.

{% include img.html src="/images/2019/09/webcam_logitech_c920pro.jpg" alt="The Logitech C920 Pro halfway through disassembly" aspect_ratio=56 %}

_The Logitech C920 Pro halfway through disassembly_

Make sure to connect the webcam and check if it still works after disassembly. At this point I noticed the blue lights, that I decided to cover up with tiny amounts of poster tack. You can see that in the part overview picture below.

# Spectroscope assembly

With all the parts printed out and not yet assembled, you will have the items shown below. The diffraction grating is already cut to size and is barely visible below its holder, and the poster tack is already applied over the LEDs.

{% include img.html src="/images/2019/09/spectroscope_disassembled.jpg" alt="The spectroscope about to be assembled" aspect_ratio=75 %}

_The spectroscope about to be assembled_

First, attach the webcam PCB to the webcam holder and use one of the screws to attach it firmly. make sure the cable is placed in the cutout and is looped over the camera out of view. Then, use two of the screws to mount the holder at the back two holes in the case. The two half rings should point to the back of the case. Dont tight the screws too firmly yet, as we need to be able to rotate the bracket to adjust the angle. Now we can mount the grating holder with two of the screws as well. I would suggest waiting with putting the grating in yet, as it's very sensitive to dust and the substances from your fingers. The two half rings should point forwards. Don't tighten these screws either yet. Now we are ready to assemble the slit holder. Bend the razor blades slightly and click them in the slit holder. The slit holder should fit in the case tightly. If you want to be able to adjust the slit size easily after you close the lid you should point the razor blades to the outside. I decided not to for now, but maybe will change that later on. 

{% include img.html src="/images/2019/09/spectroscope_assembled_without_lid.jpg" alt="The assembled spectroscope without the lid" aspect_ratio=100 %}

_The assembled spectroscope without the lid_

As we're almost done I decided it was time to insert the diffraction grating. As you can see it mas very dusty when I took these pictures, so right before I mounted the lid I replaced it with a fresh cutout. As I don't have any un-powdered medical gloves I decided to use my winter gloves to handle the sheet as not to place fingerprints on it. I noticed before that the substances on my finger were not removable after I touched the sheet, so I suspect that it went into the sheet. I will take a look at it under a microscope later on.

{% include img.html src="/images/2019/09/spectroscope_front_view.jpg" alt="Spectroscope inside view" aspect_ratio=75 %}

_Spectroscope inside view. You can see the diffraction grating diffracting the colors of the PCB._

With the webcam attached to a computer we can now calibrate the angles of the components and the size of the slit. Point a flashlight at the spectroscope or hold the spectroscope to another light source. If the vertical alignment is incorrect it's probably because one of the components isn't properly attached to the bottom plate. Horizontal alignment can be adjusted by changing the angles of the slit and camera. The height or intensity of the spectrum can be calibrated by changing the size of the slit. After tying a knot in the cable to add some prevention of pulling the camera out, we can screw on the lid. Make sure the knot is inside the case and there is sufficient clearing for the cable.

{% include img.html src="/images/2019/09/spectroscope_assembled.jpg" alt="The assembled spectroscope" aspect_ratio=56 %}

_The assembled spectroscope_

# The result

Now me can finally see what this thing can do! I wont go into any exciting stuff yet, that's for a later post. But let's what what normal daylight looks like spectrum-wise.

{% include img.html src="/images/2019/09/spectrum_daylight.jpg" alt="Spectrum of daylight" aspect_ratio=56 %}

_Spectrum of daylight_

The first thing you probably noticed is the (mostly purple) light pollution. There seems to be an issue with bright light and reflection inside the case, so I am planning to paint the inside with a non reflective coating. The reason it tends to purple is because the filament has purple/brownish hue in small quantities. The second thing you may think is: You made a rainbow, so what? Daylight is constructed of all wavelengths, so let's look at a light source that's not! My noble gas lamps for calibration still haven't arrived so that's not an option. I decided to look at my computer screen as it's made out of millions of red, green and blue LEDs tuned to the cones in our eyes. You can clearly see the distinction between the three in the spectrum below!

{% include img.html src="/images/2019/09/spectrum_screen.jpg" alt="Spectrum of computer screen" aspect_ratio=56 %}

_Spectrum of computer screen_

That's it for this post. Next up: experiments!