# Keyboard PCB Guide

So you want to make a PCB for a keyboard? Don't know where or how to start? Well you've come to the right place!

## Setting Up

We're going to need [KiCad](http://kicad-pcb.org/). Download it, install it, and you should be ready to go!

... almost.

We're going to want some libraries, too. I like to use Hasu's keyboard_parts [component library](https://github.com/tmk/kicad_lib_tmk) and [footprint library](https://github.com/tmk/keyboard_parts.pretty). /u/techieee also has a good [switch footprint library](https://github.com/egladman/keebs.pretty).

Download all of these and we should be good to go!

## Schematics

Start up KiCad and create a new project (File > New Project > New Project). Name the project whatever you want. For the purposes of this guide, I'll be calling it "example". Very creative, I know.

We'll start by designing our schematics. Double click on your `.sch` file and you should be greeted with an empty schematic sheet:

![blank schematic screen](https://puu.sh/tlFSC/faf74e75f0.png)

Let's add our component library. At the top of the window, click on Preferences > Component Libraries. Then, click on "Add" and find the `keyboard_parts.lib` file from Hasu's library. Scroll down to the bottom of the component library list and find the library you just added. We want to move that to the top of the list, so your list should look like this:

![component libraries](https://puu.sh/tlG4e/dfa008f189.png)

Click "OK" and we're ready to go. We're about to get real technical here, so buckle up.

To start off, here's a list of basic commands:

```
m: pick the component up and move it
g: drag the component up and move it while keeping wires attached to it
c: copy the component
e: edit the component
r: rotate the component
y: mirror the component
del: delete the component
esc: abort!
```

Do Place > Component. Your cursor should turn into a pencil. Click anywhere on the sheet. Look for `ATMEGA32U4` in the keyboard_parts library:

![atmega32u4](https://puu.sh/tlGyD/49e890916a.png)

Click OK, then click on the schematic sheet again to place the component. This is our controller. Edit the component and change the reference from "U?" to "U1". This is the unique name that we're going to use to refer to this particular component.

The next part we'll want to place is the crystal, which is the part that tells the controller how fast to run. Look for the `XTAL_GND` component and place it next to the controller. Change the reference to X1.

Next, we're going to want to add 2 decoupling capacitors (`C_SMALL`). These capacitors will basically help prevent the signal to the controller from accumulating too much noise. There's a formula for determining the capacitance you need for these capacitors, but for now, we'll use a crystal with 18pF load capacitance, so these decoupling capacitors will be 22pF. Name them C1 and C2, and change their values to 22p. Also add a GND symbol to represent ground, and connect everything using the wire tool (green line on the right) like so:

![crystal](https://puu.sh/tlHHo/8621b549c2.png)

Next, we'll add decoupling capacitors for VCC, our power source. We will generally want one 0.1uF capacitor for each VCC/AVCC on the controller and one 4.7uF capacitor for UVcc. In our case, we want 4 0.1uF capacitors and 1 4.7uF capacitor, like so:

![capacitors](https://puu.sh/tlI9L/24e8f9f8bb.png)

Let's hook up a reset switch. For this, you'll want a switch (`SW_PUSH`) named SW1 and a 10k resistor for pullup (`R`) named R1. If you want to know why we want a pullup resistor and what a pullup resistor even means, [here](https://learn.sparkfun.com/tutorials/pull-up-resistors) is a good explanation from Sparkfun. But for now, here's how it should be hooked up:

![reset](https://puu.sh/tlIFI/447ea96e89.png)

You'll notice that we haven't connected it to ground yet. We will Soon<sup>TM</sup>, but first, let's put a 10k resistor named R2 on HWB/PE2 pin and connect it to ground. We want a resistor here because it tells the microcontroller that when we press the reset button, we want to go into the bootloader so that we can flash a new layout onto it!

![hwb](https://puu.sh/tlJ3y/fc56dc3b1a.png)

Next, let's add our USB port. Add the `USB_mini_micro_B` component from the keyboard_parts library and call it J1. Connect VUSB to VCC and Uvcc, and put two 22 ohm resistors R3 and R4 between the D- and D+ connections. Connect GND and SHIELD together and connect them to ground. And lastly, put a 1uF capacitor C8 between UCap and GND:

![usb](https://puu.sh/tlJnf/ac2c36306d.png)

Let's connect all the VCC connections together and all the GND connections together. Normally, you would place a capacitor between AVCC and VCC if you were using the built-in ADC (analog to digital converter), but we don't care about that for a keyboard, so just directly connect them. Here's what everything look like at this point:

![overview](https://puu.sh/tlJtk/9ebde69104.png)

Now let's build our switch matrix. For the purposes of this guide, we're simply making a nice and easy 2x2 matrix. We're going to want to use the `KEYSW` and `D` components for our switch and diode components, respectively. Just connect them like you would a handwired board, and don't forget to name them. K1 should correspond to D1, K2 should correspond to D2, and so on:

![matrix](https://puu.sh/tlJQk/329ddf6af5.png)

Now we want to connect this matrix to the controller. We'll use labels for ease (A with a green line underneath on the right). For our example board, we'll use PF0 for row0, PF1 for row1, PF4 for col0, and PF5 for col1:

![matrix controller](https://puu.sh/tlJZ1/1b38e0274b.png)

Finally, let's label all the unused pins as not connected. Use the no connect tool (blue X on the right) and click on all the unconnected pins on the controller and the ID pin on the USB port. This is also a good chance to make sure you didn't miss any VCC or GND pins earlier! Our final schematic should look like this:

![final schematic](https://puu.sh/tlK4q/329f40d3d1.png)
