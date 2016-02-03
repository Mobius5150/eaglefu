# PCB Review Tips
This file contains numerous tips to help people who are new to Eagle review their designs intelligently. It's also a good list of reminders for experienced Eagle users.

This document has four sections: 

 1. Before you begin!
 2. Schematic Review
 3. Board Review
 4. Library Component Review

## 1. Before you begin!
The very first step for creating a PCB is to **determine where your board will be made.** Why? Because the board manufacturer will publish their capabilities. Things like minimum trace width, recommended trace width, spacing, drill sizes, etc... So the first step is to know these limits so that you can design your board with those in mind. Otherwise when you go to send the board for manufacture they may come back and tell you they can't do it, and you'll feel like a fool, won't you? It's also a good idea to keep in mind cost. Some manufacturers will charge a certain amount for anything less then 5cm x 5cm or 10cm x 10cm. This is also the time to decide how many layers you'll be using (for simple boards you'll typically choose two).

### DRU Files
Most (good) PCB manufacturers will even supply you with design rule (DRU) files that allow Eagle to automatically check your board for compliance with their minimum requirements. I have a hotkey (Ctrl+Shift+D) for running design rule checks and I run them constantly. It's the best way to ensure you don't do a bunch of work and have to revisit it. It's also helpful if you are doing things at minimum-width or size.

Here's some DRU files from common hobbyist PCB manufacturers:
 - Seeed Studio [2 layer](http://www.seeedstudio.com/document/rar/Seeed_Gerber_Generater_2-layer.zip) [4 layer](http://www.seeedstudio.com/document/rar/Seeed_Gerber_Generater_4-layer_1-2-15-16.rar)
 - Itead Studio [2 layer](http://iteadstudio.com/store/images/produce/PCB/PCB%20prototype/ITeadstudio_DRC.rar)
 - OSH Park [2 layer](https://oshpark.com/LaenPCBOrder.dru)

To load a DRC file in Eagle open the board view then select `Tools->DRC->Load`. Select the DRU file and click `Check` to run the DRC check.

## 2. Schematic Review

### 2.1 Passive Component Selection and Ratings (Capacitors, Inductors, Resistors)
When you choose components keep in mind their secondary attributes. For example, you will typically choose the _value_ of capacitor first - e.g. `1uF`. However, capacitors also have voltage and ESR ratings. Here's a list of secondary attributes to check:

#### 2.1.1 Component Ratings
You often see many different values of resistors somewhat arbitrarily chosen. Do you really need a `1k`, a `1.3k`, and an `800Ohm` resistor? Or would you be fine with three easy-to-source `1k` resistors? Often the exact value doesn't matter.

#### 2.1.2 Capacitors
 - Voltage - Check the rated voltage of the capacitor. If you might have spikes make sure that the maximum voltage spike is nicely within the capacitor rating.
 - Capacitance/package - Ensure that you can physically find a capacitor of the rating you're looking for. You likely won't find a 0603 `1mF` capacitor.
 - Polarized? - Is the capacitor you have chosen polarized? Ie: does one end need to be more positive in voltage than the other? Have you got it oriented the right way? Will the voltage dip and become negative sometimes?
 - ESR - Some designs call for low-ESR capacitors (often with buck/boost converters). Does your cap need to be low ESR? Do you need to use a ceramic cap with a lower ESR than the electrolytic one you selected?
    - If you didn't know ESR effects how fast a cap will react to transients, and defines things like inrush currents (ie: how much current does the cap take in the moment you apply 5V)
 - Number of caps vs. capacitance - some designs call for 3x`22uF` capacitors close together, rather than a single 66uF (or other value) capacitor. Have you done that right?

#### 2.1.3 Inductors
 - Impedance - Have you checked the internal impedence of the inductor you plan to use? Many chip (ie surface mount) inductors have higher impedance than through-hole ones. Is it compatible with your design? Many buck/boost converters have guidelines on this.
 - Package selection - Inductors come in many funky shapes and sizes. Ensure that you have picked yours out and are 100% sure the footprint you've selected will work. If you didn't make the footprint consider taking a look at it in the library editor to confirm dimensions. See section 3 on library component review.
 - Current rating - This is tied to the impedence - have you considered what the maximum current through your inductor is?
 - Back-EMF - You can't change the current over an inductor instantly. So what happens if you disconnect a load from an inductor (like with a switch)? You get a massive spike of _negative voltage_. If this could happen in your circuit consider a protection like a freewheeling diode.

#### 2.1.4 Resistors
 - Wattage - Double check the wattage of your resistor, and how much current you plan on pushing through it. Keep in mind that most chip resistors can only handle <=1/10W compared to many through-hole resistors which can often handle 1/4W. Don't make assumptions - check the datasheet.
 - Units - When you put in `10m` did you mean `0.010 Ohms` or `10,000,000 Ohms`? Consider adapting a naming convention that uses abbreviations for large exponent like "Meg". This way your resistors will be labelled `10m` for the first case, or `10Meg` for the second.

### 2.2 Component Net Connections and Junctions
It is possible to have components that _look_ like there are nets connecting to their pins, when in actuality they aren't connected. Want an easy way to test? Grab the component with the move tool and drag it a little. Make sure all of the nets (the attached green/gray wires) move with the component. If anything doesn't, it's not connected. Once you're satisfied simply hit the `Escape` or `Esc` key to cancel the move without making any changes.

Whenever you have more than one thing connecting in one place lay down a junction -- this ensures that everything at that spot is connected together, and the little dot on top of the wire tells everyone that those are supposed to connect.

### 2.3 Net Names
Whenever you have a net coming off of a pin, but not obviously connecting to another component place a label on it. Why? Because _you should name all such nets with a descriptive name_. Aside from being good style, lets you easily determine whether that net is supposed to be empty (it has a name assigned) or whether you simple haven't gotten to routing that yet.

### 2.4 Unconnected Pins
This is an item of style, but I always recommend placing the text "NC" next to any pin that should remain unconnected. This tells later viewers that you intended for that pin to be unconnected, and haven't simply forgotten to route it.

### 2.5 Power and Logic Voltage Levels
Have you checked that all the power and logical voltage levels of your devices are compatible? Not all devices can do I2C or serial at 5V. Some can only do 3.3V or another voltage. Confirm this for every input pin on your board. As a general rule, no input should have a higher voltage than the main voltage input for the device, or a lower voltage than the ground for the device. There are expections to this rule - and if they exist they should be explicitly mentioned in the datasheet.

### 2.6 Package Selection
Ensure that you have explicitly selected the packages (the physical footprint) for all of your parts. E.g. Did you just use a generic footprint for a resistor/transistor/inductor without checking the sizing? If you are using a standard footprint like SOIC or SON ensure that you are knowingly using either North American or European standards as they have different pin spacing. Consult the part datasheet and the library editor (see section 3) when in doubt.

Also be wary of packages without exposed pins (such as BGA) these can be extremely difficult to solder, and you won't be able to tell if the alignment is correct without an x-ray machine.

### 2.7 Differential Pairs
Common components such as USB and HDMI require you to route signals as differential pairs. This is made easy in Eagle by naming the signals correctly. Suffix the signals with `_N` and `_P` respectively, and the board will detect that they are differential pairs and help you with routing them. See [this post on differential pairs in Eagle](https://www.element14.com/community/thread/14788/l/differential-pair-signals-and-buses?displayFullThread=true) and see section 3.8.

## 3. Board Review
This section contains tips and things to consider when making/reviewing a PCB. DO NOT skip reading the first one - it's the biggest problem any board will face.

### 3.1 Design for the Physical World
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

### 3.2 Trace Width and Power Considerations

### 3.3 Planes and Polygons
Discuss different planes and polygons, assigning rank, thermals, orphans.

### 3.4 Silkscreen and Component Labels
Discuss the normalize-text.ulp. Mention the smash command to re-arrange component labels and delete names/values.

### 3.5 Board Labelling
Place a descriptive name and revision number on each board. I also like to place my name on it.

### 3.6 Component Rotations
Choose 1 or two rotations for components. They should be either vertical, -45 degrees or +45 degrees. Unless you're going for something artistic don't use other rotations, and stick with 1 or 2.

### 3.7 Component Proximity
Consider the proximity of capacitors and resistors to input pins. If it needs to be very close ensure that it is. Consult reference designs for power components, microprocessors, and anything high-frequency.

### 3.8 Differential Pairs
To route a differential pair on an Eagle board ensure they are named properly (section 2.7). Then in the board layout select one of the wires of the signal. The other will start routing alongside it, with the distance set by its net class. To route just one of the signals hit the escape key. To add a meander (which helps ensure the lines are the same length) use the meander tool. This tool tells you the difference in length between the signal and adds a meander if neccesary. You can set the acceptable difference in length in your DRC settings.

### 3.9 Controlled Impedence


### 3.10 Antennas
Antennas are a bowl of worms. Be very careful as they often require controlled impedence, precise routing, and placement clear of top/bottom/internal copper for good reception. Seriously, consult a pro, or at least an application note from a reputable company.

### 3.11 Analog Signals and Grounds
Keep any noisy analog signals and grounds away from digital ones. If traces are close the parasitic capacitance from the analog signals will muddle the digital once and cause big problems, especially with high-frequency lines.

### 3.12 I2C (TWI, Two-Wire Interace, Inter-Integrated Circuit Protocol)
This protocal has alot of synonymous names, but they're all the same. Check that:

 - Both SDA and SCL lines have pullups resistors. I2C doesn't work without them.
 - Both SDA and SCL lines should go through roughly the same places to ensure similar electrical properties.

## 4. Library Component Review

