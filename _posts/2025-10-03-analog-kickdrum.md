---
layout: post
title: Designing an Analog Kickdrum Synthesizer From Scratch
categories: [Electrical Engineering, Circuit Design]
---
While researching simple modular analog synthesizer projects, I came across [Moritz Klein's](https://www.youtube.com/@MoritzKlein0) tutorials on various analog synthesizer building blocks. After watching a couple of his videos, I learned I'd need quite a few components before I could really try my hand at synthesizer design. But I couldn't ignore my desire to build something with my new knowledge, so I began drafting my next project while I waited for my ICs and transistors to arrive.

# The Kickdrum

<img src="/images/kickdrum-breadboard.jpeg" alt="My project breadboard">

In this post I will detail the process of designing and engineering my analog kickdrum! I will break down the vital building blocks, my circuit design choices, and hopefully teach a couple things about synth design along the way.

<audio controls>
  <source src="/assets/kickdrum-soft.mp3" type="audio/mpeg">
  Your browser does not support the audio element.
</audio>

<audio controls>
  <source src="/assets/kickdrum-hard.mp3" type="audio/mpeg">
  Your browser does not support the audio element.
</audio>

_May only be audible on headphones_

#### Left Audio: Soft kick \|\| Right Audio: Hard kick

## But What Makes a Kick a Kick?

The iconic kickdrum is the base (and the bass) of any modern drum groove; But what does it mean to go about creating one from scratch? Let's analyze a basic kick waveform to determine what our circuit needs to emulate:

<img src="/images/kickdrum-waveform-example.png" alt="A kickdrum waveform">

In its simplest form, a kick is a simple sine wave with some clever modulation. The beginning of the kick, called the "transient," delivers a punch with its high amplitude and frequency. As the wave continues, the frequency settles into a boomy low frequency and the amplitude begins to trail off until it returns to 0. Traditionally, this could be achieved with a voltage-controlled oscillator (VCO) that generates the sine wave and a voltage-controlled amplifier (VCA) that attenuates the signal. Then, two envelope generators in tandem could sculpt the control voltages for each component and our kickdrum is complete! Unfortunately, these modules are bulky and without ICs or transistors, a VCA is certainly out of reach. This is where the applications section of my EECS textbooks come in handy:

## The Bridged-T Oscillator

<img src="/images/bridged-t-oscillator.jpg" alt="A typical bridged-t-oscillator schematic">

The bridged-t oscillator is a simple RC op amp based oscillator that produces decaying oscillations like those of a kickdrum in response to trigger impulses. As you can see in the image above, its output takes care of two of our big problems:

- Generating the sine wave (traditionally achieved with a stable VCO) and
- Shaping the amplitude (which typically requires a VCA _and_ an envelope generator)

_(If you don't really care how it works and you just want to see the rest of the project, skip this next part)_

The bridged-t oscillator functions due to the op amp's desire to equalize the voltage across its inputs. When a trigger voltage change is detected at the noninverting input of the op amp, the op amp creates a voltage at its output. This voltage pushes through the capacitors, initially satisfying the op amp. But C2 quickly begins to drain through R1, reducing the voltage that reaches the noninverting input. This spurs a feedback loop where the op amp continues to compensate by raising its output voltage while R1 drains more and more current. The only loop breaks due to the bridge resistor R2, which has allowed a small amount of current to reach C1 throughout this whole process. At some point, C1 builds more voltage than C2, meaning the op amp has overcompensated and subsequently the process begins once again, but in reverse. Each time it reaches a peak, it loses a bit of momentum to the ground through R1, causing the oscillations to trail off in a pattern much like that of a kick.

# The Design

Though it may seem that all I need to do is slap an op amp and some simple components together, the oscillator is only the jumping-off point for this design. This is mostly due to my design limitations, being:

##### 1. Power supply (biasing)

Most synthesizers function using a bipolar 12V voltage scheme, meaning they use voltages as low as -12V and as high as 12V by creating a "virtual ground" at the center of a 24V power supply. I have no such power supply, nor do I have the ICs required to sustain such a system. This means I must use what I have laying around, which is a bunch of 9V batteries. This is important because to process audio signals with op amps, the signal must be able to fit within the bounds of the op amp. Therefore my audio signal must be _biased_ to center on ~4.5V so the op amp has room to process it. Once processed, I can "AC couple" my signal to ground, removing its bias. This concept is central to synthesizer design even when a full bipolar power supply is available.

<img src="/images/biasing-example.png" alt="A comparison between my biased signal and my AC coupled signal.">

As you can see in my design, the biased green signal centers on ~4.5V, while the blue AC coupled output signal centers on 0V.

##### 2. Triggering system

<img src="/images/gate-vs-trigger-example.png" alt="A gate signal vs. a trigger signal">

As mentioned earlier, analog synthesizers respond to voltage "gates" and "triggers." Typically, a sequencer sends triggers to the drum machine, allowing you to write rhythmic patterns on the tempo grid. I have no such source, so I must create these triggers myself. I decided on a push-button whose signal resembles a gate. However, my kickdrum reacts to changes in voltage, meaning both the button press and release would result in a kick. My challenge is to design a circuit that could turn the rising edge of a gate into a trigger whilst trying my best to prevent the button release from triggering as well.

##### 3. No transistors

No access to transistors makes my options for fancy features quite limited. In fact, since synthesizer pitch is controlled by voltage signals, transistors are necessary to turn those voltage signals into a variable resistance that results in frequency change. This means I actually cannot emulate the pitch swoop of a typical kick in this early design; However, I am definitely returning to add these features later.

## Putting It All Together

<img src="/images/flowchart.png" alt="A block diagram of my design">

After considering my various goals and limitations, I drew up this block diagram to direct my efforts. I'll give a quick explanation of each block and how they create the sound. I've also annotated a schematic to streamline my explanation:

<img src="/images/kickdrum-schematic.png" alt="An annotated schematic">

### Voltage Buffers

The first step was creating two high-impedance voltage sources out of my 9V power supply:

- The virtual ground (4.5V) allows me to bias the bridged-T oscillator to work within 0-9V
- The trigger voltage (~5V) will wait on the other side of the push-button to be released on press

_Note: These voltages could not be created with resistors alone, as the gate-to-trigger converter I designed is too low-impedance and would pull the voltages too much. That is why I used op amp buffers here._

### The Push-Button Trigger

<img src="/images/gate-to-trigger-example.png" alt="My gate-to-trigger converter">

Now, once the push-button is pressed it jumps from the 4.5V virtual ground to the trigger voltage, as shown by the red gate signal above. The gate initially pushes through capacitor C1, but this voltage quickly drains through a nearby resistor. Diodes on the other side of the capacitor ensure that the voltage at the op amp input stay as stable as possible when the button is released. This results in the light green signal shown above. 

To quickly recap, a button press pushes a rising edge, spiking the capacitor and causing a trigger. On button release, the diode clamps the voltage, stifling the would-be release trigger.

In the simulation, there is still a noticeable voltage drop on button release. But in practice the release trigger is indistinguishable from noise. This is what two button presses (and releases) looks like on my oscilloscope:

<img src="/images/kick-trigger-osc.png" alt="My gate-to-trigger converter on the oscilloscope">

### The Oscillator & the Decay Extender

I've already gone into great detail about the oscillator, but on its own the signal decayed much more quickly than I liked. So, I added a "decay extender" which uses another op amp to re-introduce some momentum to the bridge network. It reads the bridge’s signal and injects a smaller version back into the network, effectively reinforcing the oscillation and extending the decay (I learned this method from Moritz Klein). Here's what this looks like on my oscilloscope:

<img src="/images/kickdrum-osc.png" alt="My kickdrum on the oscilloscope">

### AC coupling, Tone Control, and Out

Finally, the circuit has created a signal that almost look like a kickdrum. There are only two more things to consider before passing it to a pre-amp:

- The signal is still biased: using a high-pass filter with an extremely low cutoff allows me to passively eliminate DC bias.

_Note: I used a 1µF capacitor with a shunt 15kΩ resistor; Cutoff frequency fc = 1/(2*pi*R*C) = 10.61 Hz, just below human hearing._

- The signal is pretty raw and harsh; especially the transient is very clicky. I won't be able to eliminate this effect completely, but an adjustable low-pass filter will allow me to tone it down.

_Note: I used 8.6nF capacitance with a 100kΩ potentiometer, allowing me to move the cutoff between ~19kHz and 190Hz._

Once these simple filters are applied, it's finally--

# Complete!

I really enjoyed this project. Everything from scanning 10-year old forum posts online to schematic-prototyping and breadboarding was extremely rewarding, and it only makes me even more excited to tackle bigger projects once my arsenal expands. But I'm not quite ready to let go of this project just yet; I still need to

- Add transistor-based pitch envelope
- Add voltage-controlled accent control
- Add distortion effect to expand the kick's range
- Design more drum sounds to play along together
- Build a sequencer to write my patterns with

Until then, I hope you appreciated this breakdown of my analog kickdrum project. Thanks for reading!