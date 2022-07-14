# Lisa GALSCSI Card
A newly designed GAL-based SCSI card for the Apple Lisa.<br><br>
![GALSCSI Card](https://user-images.githubusercontent.com/16897189/178311915-48b6edca-5454-464b-a7d8-3b317f6e618e.png)
# Introduction
A few months ago, I decided to try and reverse-engineer the Sun Remarketing SCSI card for the Lisa, but I had to give up on making an exact replica of it for intellectual property reasons. So instead I ended up designing my own SCSI card that replaces all of the glue logic with a GAL16V8. I've also replaced the original EPROM with an F-RAM chip, which allows the user to reprogram the ROM straight from the Lisa without having to go through the painful process of removing the chip, using an external programmer, and reinstalling it in the Lisa, which can really come in handy when developing new software for expansion card ROMs. 

F-RAM (ferroelectric RAM) is a special type of memory that combines the fast read and write speeds of RAM with the non-volatility of a ROM, essentially giving you the best of both worlds. I originally wanted to use a 28CXX-series EEPROM for this project, but the 5ms write speed was just too slow.

# Total Price
Using [JLCPCB](https://jlcpcb.com/), I was able to get 5 SCSI cards for $19.10. Shipping will add to this, but not by much if you choose the really slow option. Since the minimum order quantity is 5 boards, you can just keep the others for future Lisa projects or give/sell them to others in the community who might want some. The parts cost comes out to $57.49, making the total cost of the card around $76.59. Of course, these prices will fluctuate over time, but this should give you a decent idea of how much things will cost.


# PCB Fabrication
I use [JLCPCB](https://jlcpcb.com/) for all of my boards due to the low price and high quality, but of course you can use any manufacturer that you want. There's really nothing special to say about fabricating the SCSI card, except that you might want to select the "Gold Fingers" option in order for the edge connector to be gold plated. This will likely increase the longevity of your card and makes it look cooler, but it also significantly increases the price and I just chose to use the standard silver fingers on my boards because of this. The silver fingers seem to be pretty durable though; I've inserted and removed this card hundreds of times during testing and it seems no worse for wear.

# Board Customization
I designed this SCSI card using EasyEDA. If you want to customize the board to your own needs, the design files can be found [here](https://oshwlab.com/AlexTheCat123/custom-lisa-scsi-card).

# Required Parts
Check [this DigiKey cart](https://www.digikey.com/short/3rd0c23v) for most of the required parts. Everything else can be found on eBay: The F-RAM chip (an FM1608) can be found [here](https://www.ebay.com/itm/401858081542), the 5380 SCSI controller chip can be found [here](https://www.ebay.com/itm/164880421011), and the two  resistor networks for termination can be found [here](https://www.ebay.com/itm/223907117513).


# Assembly and Use
- Assembly should be pretty simple; the silkscreen on the board tells you exactly where each component goes. 
- After you've got the card put together, you'll need to write the SCSIGALEquations.jed file to your GAL16V8. Since there are a bunch of different tools for programming GALs, I'm not going to go into much detail about how to do that here. If you're using a TL866 and Xgpro for this process, it's pretty much the same as writing an EEPROM: select your chip, load the JEDEC file, and program the device. Note that I've had some trouble getting the minipro command line tool for the TL866 to program the GALs, so try the GUI-based Xgpro if minipro won't work.
- Once the GAL is programmed, we need to program the F-RAM chip that replaces the EPROM on the original card using the F-RAM.bin file. Assuming you're not using a custom ROM like Quickboot, this file's contents should just be a $1B in the second byte and zeros everywhere else. You can put the chip in an EEPROM programmer to program it, but I would recommend using my serial programmer tool that I'll discuss in a second.
- IMPORTANT: If you do end up using an EEPROM programmer to burn the F-RAM, make sure that it doesn't write protect the chip when it's done programming! If it does, you won't be able to modify the chip's contents once it's back in the Lisa. I made this mistake and spent hours trying to figure out why my Lisa couldn't write to the chip anymore!
- To modify the contents of the F-RAM once it's installed in the Lisa, just write to the low decode of the slot that it's installed in just like you would with any memory location. This can be automated for you with my [serial loader program](https://github.com/alexthecat123/Lisa-Expansion-ROM-Programmer), which automatically detects what slot your F-RAM is installed in and lets you send a 4K ROM image straight to the card over the Lisa's serial port.
