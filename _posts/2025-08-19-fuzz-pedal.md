---
layout: post
title: My First DIY Guitar Pedal
categories: [Electrical Engineering, Reflection]
---
Within the rich universe of passionately engineered synthesizers, the humble [Stylophone](https://stylophone.com/product/stylophone-s-1/) first introduced me to the world of analog audio gear. 

Functionally, the Stylophone is extremely simple. With just one setting for vibrato (Two, if you count volume), the Stylophone's contacts make for a quirky, thin sound similar to the expressive [Otamatone](https://otamatone.com/). Unlike the Otamatone, however, it's the physical properties of the metal stylus and contacts that allow for the Stylophone's unique expression. As it slides across the keys, the stylus briefly breaks electrical contact to pass along each note creating a jumpy, discrete glissando between notes. When the stylus presses lightly against the contacts, you can hear the electrical connection stutter like a waver in a singer's voice.

<img src="/images/stylophone.jpg" alt="A stylophone.">

###### By MenRmemes - Own work, CC BY 4.0, [https://commons.wikimedia.org/w/index.php?curid=142164602](https://commons.wikimedia.org/w/index.php?curid=142164602)

From a computer music background I *moderately* understood how computer programs process digital signals. But I quickly became fascinated by the physical electronic circuits that process sound as a voltage signal, relying on the same universal laws as my homework. So of course, I had to try my hand at the art of analog audio gear. Today I will reflect on my earliest project: A fuzz distortion pedal.

# The Dream Fuzz

---

<img src="/images/fuzz-pedal-1.jpg" alt="My fuzz pedal build.">

My first opportunity to experiment with analog audio gear was building a guitar pedal modeled after the popular [Big Muff Pi](https://www.ehx.com/products/big-muff-pi). In the world of guitar pedals, distortion effects tend to be simpler in design. For this reason I ordered a premade PCB from [PedalPCB](https://www.pedalpcb.com/product/dreamfuzz/) and an enclosure from [Tayda Electronics](https://www.taydaelectronics.com/) (Tayda is an amazing resource for guitar pedal gear, as they offer many simple components as well as an enclosure drilling service). 

By selecting a premade effect I was able to establish some fundamental skills like

- reading schematics and datasheets,
- considering my electronics in 3D space
- familiarizing myself with DIY guitar pedal spaces
- studying op amp ICs, and
- sourcing components online

before requiring a deep understanding of guitar pedal design. I made my *first-ever* bill of materials in Excel and prepared to solder those components to my *first-ever* PCB. 

# The Process

---

<img src="/images/fuzz-pedal-2.jpg" alt="My fuzz pedal build, now showing the internals of the build.">

My soldering experience up to this point was solid, but purely academic. I always had someone nearby to ask for guidance and the materials were always provided for me. Thus, this project marked my first truly independent solder work. And I feel that I did quite well: I responsibly utilized flux to ensure the solder flowed to both sides, I took decent breaks between soldering to ensure no component experienced too much heat (especially the op amps and diodes!), and I cleaned my PCB with high-concentration alcohol to remove any leftover flux. I found the process therapeutic-- that is, until I discovered my first major mistake.

## Mistake 1: The Missing Capacitor

Even after cross-checking my BOM with the schematic and double, triple, even *quadruple* checking my online cart for all the correct components, I managed to forget the 120nF capacitor. Since I was not planning on ordering parts again and ordering just one component does not make fiscal sense (most sellers don't even let you purchase less than $5 worth of parts) I decided to emulate a 120nF capacitor using the basic attributes of capacitors in series/parallel. The series combination of a 100nF and 150nF is equivalent to a 60nF capacitor (C1\*C2/(C1+C2)). With two of those in parallel, their equivalent capacitance adds to 120nF! 

Of course, it's always preferable to use individual capacitors because combinations like this tend to be less accurate and reliable. But for the sake of this casual DIY project, this was a nice way to apply basic physics principles to my project. Above all else, I was proud that I didn't let this bump in the road stop me.

## Making Sense of the Schematic

Meanwhile, I made my best effort to learn the design choices of the pedal and I began to see how segments of the circuit interact to shape the sound of the pedal. For example, the segment formed by R1 and C1 (At the left, next to "IN") is a **high-pass** network with corner frequency ~19Hz, at the lower end of human hearing. The purpose of this segment is to filter out any DC bias that might come from the guitar without messing with important frequencies to prepare the signal for processing.

<iframe src="/assets/Dream-Fuzz.pdf" width="100%" height="600px" style="border: none;">
    Your browser does not support PDFs. 
    <a href="/assets/Dream-Fuzz.pdf">Download the PDF</a> instead.
</iframe>

This preprocessed input is then boosted, inverted and biased by the inverting op amp network formed by IC 1.1:

- R3 and R2 split the 9V battery input, creating a node of 4.5V at the noninverting input to center the signal in the op-amp's range of 0V-9V.
- C2 is a large electrolytic capacitor attached in shunt to this node, smoothing any minor instability in the battery's provided voltage.
- The ratio between R1 and R4 provides gain to the input signal.
- The op amp references the signal against the noninverting node voltage, resulting in an inverted signal centered on 4.5V.

The primary distortion sound of the pedal occurs at IC 2.1, where the op amp pushes the signal through a soft clipping diode network. The network effectively *smushes* the sound, giving it harmonics and a beautiful, aggressive character.

At this point, I completely finished soldering the components to the PCB. All that was left was to assemble the PCB, the footswitch, the audio jacks and the battery inside the enclosure. Except for one minor detail...

## Mistake 2: No Space For A Battery!?

Typical guitar pedals run on 9V DC Power, supplied by either a battery or an AC-to-DC power adapter. I opted for the battery, assuming that it would fit inside the enclosure. Turns out pots, audio jacks, and footswitches are quite big and by the end of the build I realized that there wasn't enough room for a battery within the enclosure. Thankfully, I had chosen to drill the DC input jack anyway, allowing me to leave the battery outside the enclosure while it's closed.

Upon completing the final assembly I rushed to plug in my 9V battery and 1/4" cables-- I stomped the footswitch and... nothing

I quickly unscrewed the enclosure, checked each capacitor with a multimeter, switched out the op amps, and checked continuity across vital connections. Turns out, I mixed up my input & output jacks. *Phew!*

# Conclusions and Future Plans

---

The pedal sounds amazing. I love enabling the tone knob and using it to boost the low frequencies, emulating a classic psychedelic rock sound. My friends, who are far better at guitar than I am, have used the pedal in conjunction with reverb, flangers, and chorus to create beautiful, edgy soundscapes. While I don't have any recordings of it yet, I hope to include some in this article in the near future.

Without a doubt, the most important lesson I learned from this project was the value of personal hands-on. As much as I love to study the theory, it's hands-on learning that really allows me to make mistakes and grasp concepts like space planning and the subtleties of component selection. And as much as I enjoy collaborating in a group, it is important for me to venture alone so that I can bring a unique experience to each project team.

For this pedal I plan on:

- **Painting it:** I want to give the pedal a personal design, as well as useful labels for each knob (especially the input & output locations)
- **Replacing the battery:** The current design is a fragile, temporary workaround asking to be broken. Before anything bad can happen, I want to replace the battery with a DC input jack.

As for future projects, stay tuned to see how these experiences educate my work! And again, thank you for reading.

---

## Bonus Mistake: Resistor Standards

Resistor wattage ratings tell you the *maximum power dissipation* of a resistor before potentially overheating or burning out. Resistor tolerances tell you how precise the advertised resistance value may be. High wattage resistors are bigger, while blue resistors tend to be more precise than beige ones (1% vs. 5% tolerance). My mistake, while not fatal, arises from my ignorance of these resistor standards before I ordered them. While the typical resistor in a guitar pedal does not experience enough current to risk burning out common 1/4W resistors, certain applications such as power supply may prefer the comfortable margin of a 1/2W or even 1W resistor. More precise resistors can reduce noise and will better replicate the intended behaviors of the pedal. The resistors in my pedal are mostly beige (less precise) and a mix of 1/2W and 1/4W resistors, chosen indiscriminately.