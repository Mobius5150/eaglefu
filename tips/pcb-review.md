# PCB Review Tips
This file contains numerous tips to help people who are new to Eagle review their designs intelligently. It's also a good list of reminders for experienced Eagle users.

Have something to add? Well thought-out and justified PR's are most welcome.

## 0. Before you begin!
The very first step for creating a PCB is to **determine where your board will be made.** Why? Because the board manufacturer will publish their capabilities. Things like minimum trace width, recommended trace width, spacing, drill sizes, etc... So the first step is to know these limits so that you can design your board with those in mind. Otherwise when you go to send the board for manufacture they may come back and tell you they can't do it, and you'll feel like a fool, won't you?

### DRU Files
Most (good) PCB manufacturers will even supply you with design rule (DRU) files that allow Eagle to automatically check your board for compliance with their minimum requirements. I have a hotkey (Ctrl+Shift+D) for running design rule checks and I run them constantly. It's the best way to ensure you don't do a bunch of work and have to revisit it. It's also helpful if you are doing things at minimum-width or size.

Here's some DRU files from common hobbyist PCB manufacturers:
 - Seeed Studio [2 layer](http://www.seeedstudio.com/document/rar/Seeed_Gerber_Generater_2-layer.zip) [4 layer](http://www.seeedstudio.com/document/rar/Seeed_Gerber_Generater_4-layer_1-2-15-16.rar)
 - Itead Studio [2 layer](http://iteadstudio.com/store/images/produce/PCB/PCB%20prototype/ITeadstudio_DRC.rar)
 - OSH Park [2 layer](https://oshpark.com/LaenPCBOrder.dru)

## 1. Schematic Review

### 1.1 Component Ratings
When you choose components keep in mind their secondary attributes. For example, you will typically choose the _value_ of capacitor first - e.g. `1uF`. However, capacitors also have voltage and ESR ratings. Here's a list of secondary attributes to check:

#### 1.1.1 Capacitors
 - Voltage - Check the rated voltage of the capacitor. If you might have spikes make sure that the maximum voltage spike is nicely within the capacitor rating.
 - Capacitance/package - Ensure that you can physically find a capacitor of the rating you're looking for. You likely won't find a 0603 `1mF` capacitor.
 - Polarized? - Is the capacitor you have chosen polarized? Ie: does one end need to be more positive in voltage than the other? Have you got it oriented the right way? Will the voltage dip and become negative sometimes?
 - ESR - Some designs call for low-ESR capacitors (often with buck/boost converters). Does your cap need to be low ESR? Do you need to use a ceramic cap with a lower ESR than the electrolytic one you selected?
    - If you didn't know ESR effects how fast a cap will react to transients, and defines things like inrush currents (ie: how much current does the cap take in the moment you apply 5V)
 - Number of caps vs. capacitance - some designs call for 3x`22uF` capacitors close together, rather than a single 66uF (or other value) capacitor. Have you done that right?

#### 1.1.2 Inductors
 - Impedance - Have you checked the internal impedence of the inductor you plan to use? Many chip (ie surface mount) inductors have higher impedance than through-hole ones. Is it compatible with your design? Many buck/boost converters have guidelines on this.
 - Current rating - This is tied to the impedence - have you considered what the maximum current through your inductor is?
 - Back-EMF - You can't change the current over an inductor instantly. So what happens if you disconnect a load from an inductor (like with a switch)? You get a massive spike of _negative voltage_. If this could happen in your circuit consider a protection like a freewheeling diode.

#### 1.1.3 Resistors
 - Wattage - Double check the wattage of your resistor, and how much current you plan on pushing through it. Keep in mind that most chip resistors can only handle <=1/10W compared to many through-hole resistors which can often handle 1/4W. Don't make assumptions - check the datasheet.

## 2. Board Review
This section contains tips and things to consider when making/reviewing a PCB. DO NOT skip reading the first one - it's the biggest problem any board will face.

### 2.1 Design for the Physical World
Most electrical-type people that find themselves designing PCBs often forget one very basic, obvious, but easy to miss fact about their design: it will ultimately become a physical object and will be highly constrained by things like dimensions.

**You need to consider how you will physically interact with your design.**

Problems of this type are often concentrated around the following elements:
 - Dimensions - It's easy to zoom in on a CAD tool and lose sight of how big (or small) a part is. Do you know how big your design is? Take a ruler and draw the dimensions on a piece of paper. Can you easily handle it? Does it need to be that size?
 - Switches - Are your switches accessible to a human finger? Consider the dimensions of the board, where the switch is pointing, etc...
 - Connectors - Check all the same things as switches - are your connectors accessible? Have you considered how/where you will route the wires going from the connector? Will it be a mass of wires?
    - Are your connectors standard sizes/spacings? Just because you can make a 7 pin 0.11" connector in Eagle doesn't mean that you can find that connector, premade at least.
    - If you plan on making your own connectors do you have proper crimping tools, and know how to use them? Without these tools (and even with them) custom connectors can be horribly unreliable.
    - Is the other end of the connector larger than the board-side connector? Many connectors (like molex) have tabs or prongs on the outside of the housing. What this means is that when the connector is attached _it will take up more space on your board_ so check that your connectors aren't too close together.
 - Mounting - Do you have a plan for how the board will be secured relative to it's surroundings? Does it have screw holes? Do you have tabs on the edge to hook onto something? If you plan to have something close to an edge do you have components that are perhaps too close and will interfere?

## 3. Library Component Review

