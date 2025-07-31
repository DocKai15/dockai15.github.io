---
layout: post
title: How I Built My Ergonomic Mechanical Keyboard From Scratch
categories: [Electrical Engineering, Code]
---
<img src="/images/full-board.png" alt="splitty!">

Hello! In this post I want to detail my first ever complete electrical engineering project from scratch, my ergonomic mechanical keyboard, nicknamed splitty.

## Why?

I decided to build an ergonomic mechanical keyboard for several key reasons. First of all, it's a great place to begin learning concepts like PCB, CAD, and firmware design. A build from scratch uses all three skills without requiring a high level of expertise. Second, I believe ergonomic design allows engineers to dispel limiting design conventions in search of better, more creative solutions. Especially within the world of computer accessories, many designs (mice, keyboards, etc.) are built a certain way simply because they satisfy our image of what a "keyboard" or "mouse" ought to be.

## How?

In order to explain the process of building a mechanical keyboard, I must first go over its necessary parts. Please note that the image below is just the left half of splitty, my keyboard.

<img src="/images/exploded-design.png" alt="An exploded version of my keyboard design">

1. The PCB: Acts like the skeleton of the keyboard-- It provides a physical base and all the necessary connections for each component.
2. The Microcontroller: Acts like the brain of the keyboard-- It receives, processes, and sends all signals to the computer.
3. The Case and Plate: Provide a home for the working components of the keyboard. The case houses the PCB while the plate holds each switch in place.
4. Switches and Keycaps: Provide the literal means of typing and a major component of the keyboard's aesthetic.

These key components allow for the simplest possible keyboard functionality. However, there are some features I'd like to include that require some extra thought:

- Fully Wireless: Requires a microcontroller with bluetooth functionality and a battery. Also requires a firmware that can handle bluetooth communication.
- Split Keyboard: Requires mirrored PCBs, plates, cases and two microcontrollers/batteries.
- A Number Row: Most ergonomic mechanical keyboards ditch the traditional keyboard layout entirely, opting for "layers," which allow you to switch between keyboard layouts and type with as few keys as possible. While I plan on utilizing layers to compensate for my missing keys, I want to keep my build as similar as possible to the mechanical keyboard layout I am used to.
- No Stagger: Most keyboards "stagger" their rows. For the ergonomics of my board, I want to remove this stagger and instead rotate and tent the entire keyboard.
- Low Profile: I want my keyboard to be short like a laptop keyboard. Low-profile switches and keycaps allow me to remain as short and sleek as possible.

While most of these considerations can be engineered in with a little effort, some of them require extra/unique parts: (A full BOM can be found [here](https://www.notion.so/2192c9306a1480eb9d15f3172433599d?v=2192c9306a14815d9968000c7686ca12&source=copy_link) )

- The Supermini nrf52840, a microcontroller with bluetooth and LiPo battery charging capabilities.
- Kailh Choc Brown switches for a low-profile tactile feel.
- Power switches for on/off and reset buttons.
- 3.7V LiPo batteries.

### Part 1: The PCB

The PCB provides the connections between the microcontroller, the keyswitches, and any other auxiliary features. For now, I will stick to the relationship between the keyswitches and the microcontroller. Let's take a look at how this works by looking at one switch.

<img src="/images/single-switch.png" alt="A single switch wired to a microcontroller">

In this example, a single keyswitch is wired to two pins on an Arduino Pro Micro. When the microcontroller wants to read the position of the switch it can set one pin (ex: GP26) to "HIGH" voltage (e.g. 3.3V) and the other pin (ex: GP20) to connect a pull-down resistor. If the switch is not pressed, the pull-down resistor on pin GP20 ensures that the microcontroller reads "LOW." If the switch is pressed, pin GP20 is now directly connected to GP26 and will read "HIGH." This is quite straightforward, but you may notice that if we were to scale this operation as-is, we would need quite a few pins. 

<img src="/images/left-schematic.png" alt="My left PCB schematic using the matrix">

That's where the keyboard matrix comes in. Put simply, the matrix method wires the keyswitches into rows and columns, where each row/column is then assigned a pin on the microcontroller. This allows engineers to use smaller microcontrollers better suited to the job by reducing the necessary number of pins to function. Once in place, the microcontroller sets all rows to pull-down resistors and then sequentially sets each column to "HIGH," scanning the rows of the matrix to check each switch for a keypress. To prevent as many misinputs as possible, each switch is connected to a diode, ensuring no "ghosting" occurs when multiple keys are pressed. Once the matrix schematic is built, we can begin designing the PCB.

<img src="/images/left-pcb.png" alt="My completed left PCB">

While there are plentiful online resources to make the design process as easy as possible, (like [Ergogen](https://ergogen.ceoloide.com/) ) I decided to build my design entirely from scratch in KiCad, a popular open-source PCB design application. After following a couple tutorials to understand the workflow of KiCad, I could begin designing. My vision ended up taking a similar shape to Naoki Katahira's [Lily58](https://github.com/kata0510/Lily58) for its similarity to typical mechanical keyboard layouts. My design includes a 6x4 layout with 4 thumb keys and an additional key on the pointer row. Each column's position is strategically adjusted to fit the shape and length of my fingers. This is then duplicated and mirrored for the other side.

My designs utilize Scotto Keeb's [ScottoKicad](https://github.com/joe-scotto/scottokeebs/tree/main/Extras/ScottoKicad) library of symbols, 3d models, and footprints for common keyboard components.

### Part 2: The Case and Plate

<img src="/images/left-plate.png" alt="My completed left plate">

The plate holds each switch in place before soldering to the PCB by holding them in square holes. To model the plates, I created secondary versions of the PCBs where I replaced each switch with a 14x14mm hole (necessary for Kailh Choc switches) and removed all other components to create an image of the plate.

<img src="/images/case-and-plate.png" alt="My completed keyboard design">

This was my first-ever Fusion project, so it took me quite a while to figure out the ropes. It helped me quite a bit to export KiCad's 3D model of my PCB into Fusion to ground my ideas. All I needed to include was on/off switch access, space for a battery within the case, and holes for M2 bolts. While I would make several major changes for my v2 edition, I'm still quite proud of the effective case I was able to build without any help.

#### Side Note: Mounting Styles

<img src="/images/mounting-styles.png" alt="Thomas Baart's mounting styles infographic">

It's very easy to build each component of the keyboard separately without ever considering how they will come together. Thankfully, this [cheat sheet](https://thomasbaart.nl/2019/04/07/cheat-sheet-custom-keyboard-mounting-styles/) from Thomas Baart makes keyboard mounting quite easy to understand. I ended up settling on my own version of the "Integrated Plate" mount where I left the screws visible for a unique DIY look.

### Part 3: Ordering and Assembly

The parts I designed would've made for great 3d printing practice but unfortunately I do not have access to one. This meant two things for me: I needed to order them using online services, and they needed to be *perfect*. I did as much simulation and iteration as I could on my design before ordering my parts from [SendCutSend](https://sendcutsend.com/?srsltid=AfmBOoqlO94FP7pZe-f5gklTzPy-zCSYgoJpp9qH5sLmD3wqT4UM_TGb) and [JLCPCB](https://jlcpcb.com/). I sourced the other parts (Keycaps, switches, buttons, etc.) online. There was no one-stop-shop for everything, but I recommend [typeractive](https://typeractive.xyz/) especially for split keyboards.

<img src="/images/parts.png" alt="The case, PCB, and plate">

The case is resin and the plate is aluminum (1mm thick, although kailh switches prefer 1.2mm plate thickness)

Once everything arrived, I soldered my components and assembled the case. The final thing to do was code the firmware for my keyboard and I was done!

### Part 4: The Code

The standard firmware for keyboards like mine is [ZMK](https://zmk.dev/), an open-source software built to make bluetooth integration as easy as possible. ZMK utilizes a "Device Tree" configuration system that allows me to describe the matrix, microcontroller, pins used, sensors, etc. These configurations are then compiled into the firmware automatically by Github, giving me files to flash onto my keyboard.

ZMK is particularly handy because it comes with many pre-configured layouts requiring little to no adjustments before it can be used. Unfortunately for me, since I designed this board from scratch, I needed to create my own device tree from scratch for my keyboard. This includes defining the split keyboard matrix, which microcontrollers/pins I used, and the keyboard layout I want. Here, I'll go over the most important things I learned.

<img src="/images/zmk-filesystem.png" alt="The ZMK filesystem">

The .defconfig, .conf, and .shield files contain important information telling ZMK how to treate my keyboard. For example, the .defconfig file tells ZMK that my keyboard is named "splitty" and that the left half is "central," meaning the left half communicates with the computer. The "peripheral" right half simply sends its key presses to the left half to be sent to the computer.

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

Since the the matrix and row pin definitions hold true for both halves of the keyboard, I store them in the root splitty.dtsi file. This configuration acts as a base for the keyboard, allowing the left and right .overlay files to complete the picture. Since the left and right sides use different column pins, they each get their own .overlay file, ensuring the firmware knows exactly which parts of the matrix each half covers.

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

Finally, now that I've defined the matrix, pins, and boards I'm using, I can write a .keymap file to tell ZMK what each key corresponds to. For now, my layout contains only two layers: a base layer that corresponds to typing on the keyboard normally, and a lower layer that activates when my layer keys are held.

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

When I push these changes to Github, they are automatically compiled using Github Actions, giving me two firmware files for the left and right boards. Once flashed, the boards connect to one another over bluetooth, and they immediately begin pairing.

## Reflection and Outro

<img src="/images/right-board.png" alt="splitty!">

After about a week of adjusting to my new keyboard, I am ecstatic. It's exactly what I imagined: quick, sleek, ergonomic, and most of all, unique. I love building functional projects like these and I learned a lot in this process. That being said, there are a few things I'd include in a splittyv2 project:

- A Weighted Case: While I love the resin case I designed, it's just a little too light for my liking. Even with the rubber feet, it's too easy to slide around. Next time, I'll include space in the case for little metal weights.
- A Volume Knob: My previous keyboard had a volume knob that I loved using. I'd definitely include this in my next build.
- Screens (and a heavier focus on aesthetics): While I think my keyboard is beautiful, it's definitely a bit rough around the edges. I noticed a lot of builds on online forums include little 128x32 OLED screens to cover up the microcontrollers. I think this is super creative and would be a lot of fun to implement.
- Magnets: I noticed when carrying these boards around in a bag, they're much more prone to jumbling around and potentially damaging each other than I'd like. For my next build, I think it would be cool to include magnets that allow the halves to stick together for transportation.

Thank you for reading my article on my keyboard build! In the future, I hope to update these articles to be a bit more like guides that you can follow rather than ramblings about my projects. I hope you enjoyed!