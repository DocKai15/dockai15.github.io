---
layout: post
title: How I Built My Ergonomic Mechanical Keyboard From Scratch
categories: [Electrical Engineering, Code]
---
<img src="/images/full-board.JPG" alt="splitty!">

Hello! In this post I want to detail my first ever complete electrical engineering project from scratch, my ergonomic mechanical keyboard, nicknamed splitty.

## Why?

I chose to build an ergonomic mechanical keyboard for a few reasons. First, it’s a great way to learn fundamental concepts like PCB design, CAD, and firmware. From start to finish, you get hands-on experience in all three areas without needing deep expertise. Second, many computer accessories (like mice and keyboards) follow design conventions simply because it’s what we’re used to. Going ergonomic is an opportunity to break those habits and explore better, more creative solutions.

## How?

To explain my build process, I must first go over the main components. The image below is just the left half of splitty.

<img src="/images/exploded-design.png" alt="An exploded version of my keyboard design">

1. The PCB: The skeleton of the keyboard: It provides a physical base and all the necessary connections for each component.
2. The Microcontroller: The brain of the keyboard: It receives, processes, and sends all signals to the computer.
3. The Case and Plate: Provide a home for the working components of the keyboard. The case houses the PCB while the plate holds each switch in place.
4. Switches and Keycaps: Provide the interface and a major component of the keyboard's aesthetic.

These key components allow for simple keyboard functionality. However, there are some features I'd like to include that require some extra thought:

- Fully Wireless: Requires a microcontroller with bluetooth functionality and a battery. Also requires a firmware that can handle bluetooth communication.
- Split Keyboard: Requires mirrored PCBs, plates, cases and two microcontrollers/batteries.
- A Number Row: Most ergonomic mechanical keyboards ditch the traditional keyboard layout entirely, opting for "layers," which allow you to switch between keyboard layouts and type with as few keys as possible. While I plan on utilizing layers to compensate for my missing keys, I want to keep my build as similar as possible to the mechanical keyboard layout I am used to.
- No Stagger: Most keyboards "stagger" their rows. For the ergonomics of my board, I want to remove this stagger and instead rotate and tent the entire keyboard.
- Low Profile: I want my keyboard to be short like a laptop keyboard. Low-profile switches and keycaps allow me to remain as short and sleek as possible.

Some of these features require extra/unique parts: (A full BOM can be found [here](https://www.notion.so/2192c9306a1480eb9d15f3172433599d?v=2192c9306a14815d9968000c7686ca12&source=copy_link) )

- Supermini nrf52840, a microcontroller with bluetooth and LiPo battery charging capabilities.
- Kailh Choc Brown switches for a low-profile tactile feel.
- Power switches for on/off and reset buttons.
- 3.7V LiPo batteries.

### Part 1: The PCB

The PCB provides the connections between the microcontroller, the keyswitches, and any other auxiliary features. Let's take a look at how this works by looking at one switch.

<img src="/images/single-switch.png" alt="A single switch wired to a microcontroller">

In this example, a single keyswitch connects to two pins on an Arduino Pro Micro. The microcontroller sets one pin (e.g., GP26) to HIGH voltage (3.3V). The other (GP20) reads the signal, pulled LOW via a resistor unless the switch is pressed. Then, GP20 sees HIGH. This is simple enough, but scaling to a full keyboard would require a lot of pins.

<img src="/images/left-schematic.png" alt="My left PCB schematic using the matrix">

That's why keyboards use a matrix: switches are wired into rows and columns, where each row/column is then assigned a pin on the microcontroller. This dramatically reduces the number of pins needed. Once in place, the microcontroller sequentially sets each column to "HIGH," checking the rows of the matrix for a response. Diodes are used to prevent "ghosting," which can occur when multiple keys are pressed. Once the matrix schematic is built, we can begin designing the PCB.

<img src="/images/left-pcb.png" alt="My completed left PCB">

While there are plentiful online resources to make the design process as easy as possible, (like [Ergogen](https://ergogen.ceoloide.com/) ) I decided to build my design entirely from scratch in KiCad, a popular open-source PCB design application. After following a couple tutorials, my keyboard took a similar shape to Naoki Katahira's [Lily58](https://github.com/kata0510/Lily58), including a 6x4 layout with 4 thumb keys and an additional key on the pointer row. Each column's position is strategically adjusted for finger comfort. I built the left side first, and then mirrored my design for the right side.

My designs utilize Scotto Keeb's [ScottoKicad](https://github.com/joe-scotto/scottokeebs/tree/main/Extras/ScottoKicad) library of symbols, 3d models, and footprints for common keyboard components.

### Part 2: The Case and Plate

<img src="/images/left-plate.png" alt="My completed left plate">

The plate holds switches in place for soldering. I modeled the plate by exporting a PCB layout, swapping the switches for 14x14mm cutouts (for Kailh Choc switches), and removing unnecessary elements.

<img src="/images/case-and-plate.png" alt="My completed keyboard design">

This was my first-ever Fusion project, so it took me quite a while to figure out the ropes. It helped me to export KiCad's 3D model of my PCB into Fusion as a reference. All I needed to include was on/off switch access, space for a battery within the case, and holes for M2 bolts. I'd make several major changes for a second edition, I'm still quite proud of the effective case I was able to build without any help.

#### Side Note: Mounting Styles

<img src="/images/mounting-styles.png" alt="Thomas Baart's mounting styles infographic">

It’s easy to design components separately without planning assembly. Thankfully, this [cheat sheet](https://thomasbaart.nl/2019/04/07/cheat-sheet-custom-keyboard-mounting-styles/) from Thomas Baart makes keyboard mounting quite easy to understand. I settled on my own version of the "Integrated Plate" mount where I left the screws visible for a unique DIY look.

### Part 3: Ordering and Assembly

I would have loved to 3d print these parts, but unfortunately I do not have access to a printer. I needed to order them using online services, and they needed to be *perfect*. After several rounds of iteration, I ordered my plate from [SendCutSend](https://sendcutsend.com/?srsltid=AfmBOoqlO94FP7pZe-f5gklTzPy-zCSYgoJpp9qH5sLmD3wqT4UM_TGb) and the case/PCB from [JLCPCB](https://jlcpcb.com/). I sourced the other parts (Keycaps, switches, buttons, etc.) online. For split keyboards, I recommend [typeractive](https://typeractive.xyz/).

<img src="/images/parts.JPG" alt="The case, PCB, and plate">

The case is resin and the plate is aluminum (Kailh switches prefer 1.2mm plate thickness, but mine is 1mm and it works great).

Once everything arrived, I soldered my components and assembled the case. The final step was coding the firmware.

### Part 4: The Code

The standard firmware for keyboards like mine is [ZMK](https://zmk.dev/), an open-source software built to make bluetooth integration as easy as possible. ZMK utilizes a "Device Tree" configuration system that allows me to describe the matrix, microcontroller, pins used, sensors, etc. These configurations are then compiled into the firmware automatically by Github, giving me files to flash onto my keyboard.

While ZMK has many pre-made layouts, building from scratch meant I had to define my own device tree. Here I will cover the config, pins, keyboard matrix, and desired layout.

<img src="/images/zmk-filesystem.png" alt="The ZMK filesystem">

The .defconfig, .conf, and .shield files define core ZMK behavior. For example, .defconfig tells ZMK the keyboard name and which side is central (the left side talks to the computer; the right sends keypresses wirelessly to the left):

```c
//Kconfig.defconfig
if SHIELD_SPLITTY_LEFT

config ZMK_KEYBOARD_NAME
	default "splitty"
	
config ZMK_SPLIT_ROLE_CENTRAL
    default y
	
endif

if SHIELD_SPLITTY_LEFT || SHIELD_SPLITTY_RIGHT

config ZMK_SPLIT
    default y
	
endif
```

Shared matrix and row definitions live in the main splitty.dtsi file while left and right overlays adjust column pins for each half:

```c
//splitty.dtsi
#include <dt-bindings/zmk/matrix_transform.h>

/ {
	chosen {
			zmk,kscan = &kscan0;
			zmk,matrix_transform = &default_transform;
		};
		
	default_transform: matrix_transform {
		compatible = "zmk,matrix-transform";
		rows = <5>;
		columns = <14>;
		
		map = <
			RC(0,0) RC(0,1) RC(0,2) RC(0,3) RC(0,4) RC(0,5) 			RC(0,8) RC(0,9) RC(0,10) RC(0,11) RC(0,12) RC(0,13)
			RC(1,0) RC(1,1) RC(1,2) RC(1,3) RC(1,4) RC(1,5) 			RC(1,8) RC(1,9) RC(1,10) RC(1,11) RC(1,12) RC(1,13)
			RC(2,0) RC(2,1) RC(2,2) RC(2,3) RC(2,4) RC(2,5) RC(2,6) 	RC(2,7) RC(2,8) RC(2,9) RC(2,10) RC(2,11) RC(2,12) RC(2,13)
			RC(3,0) RC(3,1) RC(3,2) RC(3,3) RC(3,4) RC(3,5) 			RC(3,8) RC(3,9) RC(3,10) RC(3,11) RC(3,12) RC(3,13)
						RC(4,3) RC(4,4) RC(4,5) RC(4,6)		RC(4,7) RC(4,8) RC(4,9) RC(4,10)
		>;
	};
	
	kscan0: kscan {
		compatible = "zmk,kscan-gpio-matrix";
		wakeup-source;

		diode-direction = "col2row";
		
		row-gpios = <&pro_micro 2 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>,
					<&pro_micro 3 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>,
					<&pro_micro 4 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>,
					<&pro_micro 5 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>,
					<&pro_micro 6 (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)>;
	};
};
```

Finally, I defined the keymap. My setup includes a base typing layer and a lower layer activated by holding a layer key.

```c
//splitty.keymap
#include <behaviors.dtsi>
#include <dt-bindings/zmk/keys.h>
#include <dt-bindings/zmk/bt.h>
#include <dt-bindings/zmk/ext_power.h>

/ {
	keymap {
		compatible = "zmk,keymap";
		
		default_layer {
			display-name = "Bas";
			bindings = <
				&kp ESC			&kp N1			&kp N2			&kp N3			&kp N4			&kp N5											&kp N6			&kp N7			&kp N8			&kp N9			&kp N0			&kp BSPC
				&kp TAB			&kp Q			&kp W			&kp E			&kp R			&kp T											&kp Y			&kp U			&kp I			&kp O			&kp P			&kp APOS
				&kp LCTRL		&kp A			&kp S			&kp D			&kp F			&kp G			&kp CAPS				&kp DEL			&kp H			&kp J			&kp K			&kp L			&kp SEMI		&kp RET
				&kp LSHFT		&kp Z			&kp X			&kp C			&kp V			&kp B											&kp N			&kp M			&kp COMMA		&kp DOT			&kp FSLH		&kp RSHFT
													&kp LALT		&kp LCMD		&kp SPACE		&mo 1					&mo 1			&kp SPACE		&kp RCMD		&kp RALT
			>;	
		};
		
		lower_layer {
			display-name = "Low";
			bindings = <
				&bt BT_CLR		&bt BT_SEL 0		&bt BT_SEL 1  		&bt BT_SEL 2   		&bt BT_SEL 3   		&bt BT_SEL 4										&kp TILDE		&trans			&trans			&kp LBKT		&kp RBKT		&trans
				&trans			&trans			&trans			&trans			&trans			&trans											&trans			&trans			&kp UARW		&kp MINUS		&kp EQUAL		&kp BSLH
				&trans			&kp F1			&kp F2			&kp F3			&kp F4			&kp F5			&kp C_VOL_DN				&kp C_VOL_UP		&kp F6			&kp LARW		&kp DARW		&kp RARW		&trans			&trans
				&trans			&kp F7			&kp F8			&kp F9			&kp F10			&kp F11											&kp F12			&trans			&trans			&trans			&trans			&trans
													&trans			&trans			&trans			&trans					&trans			&trans			&trans			&trans
			>;	
		};
	};
};
```

When I push these changes to Github, they are automatically compiled using Github Actions, giving me two firmware files for the left and right boards. Once flashed, the boards connect and pair seamlessly.

## Reflection and Outro

<img src="/images/right-board.JPG" alt="splitty!">

After about a week of adjusting to my new keyboard, I am ecstatic. It's exactly what I imagined: quick, sleek, ergonomic, and most of all, unique. I love building functional projects like these and I learned a lot in this process. That being said, there are a few things I'd include in a splittyv2 project:

- A Weighted Case: While I love the resin case I designed, it's just a little too light for my liking. Even with the rubber feet, it's too easy to slide around. Next time, I'll include space in the case for little metal weights.
- A Volume Knob: My previous keyboard had a volume knob that I loved using. I'd definitely include this in my next build.
- Screens (and a heavier focus on aesthetics): While I think my keyboard is beautiful, it's definitely a bit rough around the edges. I noticed a lot of builds on online forums include little 128x32 OLED screens to cover up the microcontrollers. I think this is super creative and would be a lot of fun to implement.
- Magnets: I noticed when carrying these boards around in a bag, they're much more prone to jumbling around and potentially damaging each other than I'd like. For my next build, I think it would be cool to include magnets that allow the halves to stick together for transportation.

Thanks for reading! I hope you enjoyed this behind-the-scenes look at my favorite project so far.