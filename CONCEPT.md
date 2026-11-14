# Open Modular Audio, Video and Control System: Public Concept Disclosure

Author: Jonathan Sieswerda, Edmonton, Alberta, Canada
Date of first public disclosure: November 13, 2026
Status: concept and early design. Published openly so that anyone may build on these ideas, and so that they cannot be patented by others.

## 1. Purpose of this document

This document publicly describes the concepts, architecture and design choices of an open, modular, accessible system for audio, video, lighting and general control. It is published as a defensive disclosure (prior art) and as an invitation to collaborate.

Where several ways of building something are described, each one is disclosed. Phrases such as "in one version" or "alternatively" describe variants that are all part of this disclosure.

## 2. Why this system should exist

Audio, video and control equipment for churches, schools, universities, community venues, bedroom studios and streamers is usually either:

- fixed all-in-one hardware that must be sold and replaced when needs grow (for example, a 2-input interface that must be replaced when a third input is needed), or
- powerful professional systems that need certified programmers and integrators for everyday changes.

Audio, video, lighting and room control also live in separate products with separate software, and most professional hardware is not accessible to blind, low-vision or motor-impaired operators.

This system aims to be:

- modular: buy only what you need and add to it one block at a time;
- low cost: run close to cost by a not-for-profit organization, with a trade-in and refurbished program;
- easy to use: a student IT worker should be able to build and configure a system, and anyone should be able to walk up and use it;
- accessible: designed from the start for screen reader users and people who navigate by touch;
- open: open-source hardware and software, open protocols, and a published way for anyone to design compatible modules;
- repairable and sustainable: screwed construction, standard parts, long product life;
- good quality and reliable enough for its target users, without the cost of broadcast-grade redundancy in every part.

The primary audience is the budget-conscious, non-specialist user, not high-end touring or broadcast, although the architecture can scale up.

## 3. System overview

The system has two families that work independently but integrate tightly:

1. The modular AV system: metal I/O modules for audio, video, lighting, control and power that join side by side or by cable, plus hubs that manage and process everything.
2. The modular control surface: small blocks with knobs, buttons, faders, displays and accessibility features, assembled into any physical controller layout.

Both families use ordinary Ethernet (RJ45) cables and connectors as their one universal cable type.

## 4. The modular AV system

### 4.1 Form factor

- Each module is 1U high (44.45 mm), about 8 inches (about 200 mm) deep, and about 37.3 mm wide, so that 12 modules fit across a standard 19-inch rack.
- Modules may be whole multiples of this width or height, and may be deeper where components require it (for example, amplifiers, better preamplifiers or hubs).
- Modules are made of metal plates screwed together with flush, countersunk screws of standard sizes, for strength and repairability. Parts are soldered as little as practical so they can be replaced.
- Each module has an RGB status LED that also lets the software identify a specific module.
- Many modules have swappable connector plates so the same electronics can offer different physical connectors (for example XLR, quarter-inch, Phoenix/Euroblock terminals or Speakon).
- Modules are reversible: the network connectors sit at the exact centre of each side, so a module can face the front or the back and still occupy the same place in the chain.

### 4.2 Joining modules

- Each module has a female RJ45 jack centred on its left and right sides.
- Adjacent modules are joined directly by a short rigid male-to-male RJ45 bridge plug. In one version, the body of the bridge plug is shaped as an alignment key (for example an H or D profile) that locates the two modules before they are fastened.
- Modules can instead be linked by any standard Ethernet patch cable, so groups of modules can be spread across a room or building.
- Modules are mechanically fastened by small metal plates that sit in recessed grooves (about 1 cm wide) at the front and back of the top and bottom of each module, with one screw into each module. The plates carry all mechanical load; the connector carries none.
- The RJ45 jacks connect to the main board by short flexible wiring and are mounted so they can move slightly (for example 0.2 to 1 mm) in each direction to absorb manufacturing tolerances, and so they can be replaced independently.
- Mounting accessories include rack ears for assembled rows, short jumper cables linking the end of one rack row to the start of the next through the space beside the rack rails, desktop feet, side and wall brackets, and multi-layer binding brackets.
- For flush in-wall installation, one version uses an in-wall chassis between wall studs that holds a standard module vertically, with a 90-degree faceplate that carries the user-facing jacks. Another version uses a dedicated slim wall-plate module.

### 4.3 Network and timing

- Each module contains a small managed Ethernet switch with at least three ports: two external (left and right) and one internal to the module's processor. Modules therefore form a daisy chain, and any module can be connected to any other.
- Gigabit Ethernet is the baseline. A 10 Gigabit tier exists for video and high-density modules.
- Clocks are synchronized with the Precision Time Protocol (PTP, IEEE 1588). The switch in each module supports hardware timestamping and preferably acts as a PTP transparent clock so synchronization stays accurate across many hops.
- Audio and video travel as RTP streams over UDP/IP. Native audio streams are designed to be compatible with the open AES67 standard wherever practical, so the system can interoperate with other AES67, Dante (in AES67 mode) and RAVENNA equipment.
- Lighting data uses sACN or Art-Net.
- Traffic is prioritized with strict-priority quality of service (PTP first, then audio, video, lighting and control, then general traffic). VLANs separate real-time media, control and general computer traffic, so one cable can serve a podium's AV, controls and computer.
- Multicast with IGMP snooping ensures a stream sent to several outputs is only forwarded toward modules that requested it, so it stops at the last interested module rather than flooding the chain.
- In one version, the hub additionally assigns each module PTP-aligned transmit windows (a "soft time-division" schedule) to smooth traffic near the hub. Where the switch supports the IEEE 802.1Qbv Time-Aware Shaper, this is enforced in hardware.
- Systems are normally built as several short chains (branches) from a hub, rather than one very long chain. Either end of a chain may also return to the hub to form a ring, protected by Rapid Spanning Tree (RSTP).
- The hub can automatically configure a list of supported off-the-shelf managed switches (VLANs, QoS, PTP, IGMP) so users do not need networking knowledge.

### 4.4 Discovery, configuration and hub-less operation

- Modules accept an address from DHCP on an ordinary network, or assign themselves a link-local address when no router exists.
- Modules announce their name and capabilities using mDNS and DNS-SD (zero-configuration networking), for example "two-channel microphone input".
- The hub maps the physical order of modules using LLDP neighbour information and shows it to the user.
- Hub-less mode: two or more modules connected and powered without a hub find each other and can pass audio automatically, acting like a digital snake. On an ordinary network, a computer can open a simple routing page to patch inputs to outputs and set hardware parameters such as gain and phantom power.
- Control uses open protocols: OSC for simple control and AES70 (Open Control Architecture) as the professional standard. SSH is available for administration and firmware.
- Each hub, and each module in hub-less mode, serves its own accessible web interface, so no app installation is required.
- Third-party control systems (for example Crestron, AMX, Control4, Home Assistant) can discover modules over mDNS and control them with OSC, AES70 or plain TCP. Ready-made drivers for these platforms can be published.
- An open SDK and published specifications allow anyone to create modules and software.

### 4.5 Power

- Modules joined side by side share a low-voltage DC power rail through the joint between them, fed by a power-in module, a hub, or a USB-C Power Delivery input on small systems.
- A module standing alone on a cable is powered by Power over Ethernet from a standard PoE switch or injector.
- In another version, modules pass PoE (including 802.3bt, PoE++) along the chain, with power-in or regenerator modules placed wherever the power budget runs out, and modules managing their power use automatically.
- A power-in module has an IEC mains input and a power supply that feeds the chain in both directions.
- An auxiliary power module provides additional low-voltage power for high-draw modules such as amplifiers and hubs, and can receive mains passed through from a power-in module.
- Outlet modules provide switched mains outlets controlled by the hub (subject to electrical safety certification).
- Battery modules in several sizes power a small system on their own or act as backup, reporting charge and runtime to the hub. Larger runtime is achieved by paralleling battery packs behind one managed output stage.

### 4.6 Reliability

Reliability is layered and scaled to need:

- Standard modules rely on short branches, rings and fast fault reporting.
- Every module sends a periodic "I am alive" message to the hub. If an active stream stops, the hub queries that module to distinguish a software fault from a dead module. Faults are reported by physical position ("between module 44 and module 46").
- Optional bypass version: an independent watchdog microcontroller monitors the main processor's heartbeat, power rails and temperature, and drives a relay that physically connects the two network ports if the module fails or loses power, so downstream modules keep working.
- Ultra-reliable version: duplicated processing and networking in one module, connected to two independent chains, with the hub selecting the healthy stream. In another version, a separate splitter device provides this for an existing module.
- Secure boot, signed firmware updates, and automatic return to the last known good configuration if a change makes a module unresponsive.

### 4.7 Internal electronics

- Every module uses a shared core design: switch, processor, power, timing and status. Only the I/O section changes between module types.
- Standard modules are manufactured as sealed products with integrated or factory-fitted core boards for lowest cost and best audio performance.
- A premium "host shell" module can be opened with tools so a skilled user can install a different personality board. The interface between boards is digital only (I2S or TDM audio and I2C or SPI control) plus bulk power (for example 5 V and 48 V). Each personality board makes its own clean analogue power. Boards are changed with power off.
- Blank core boards and blank module enclosures are sold so individuals and manufacturers can create new modules.
- Common modules are offered in 1, 2, 4 and 8 channel versions, and in several quality tiers.

### 4.8 Module catalogue

Audio inputs:
- XLR microphone input; combination XLR and quarter-inch mic/line input; stereo quarter-inch balanced line input; stereo line input with eighth-inch and quarter-inch jacks.
- Instrument (Hi-Z) input; instrument input with a through output to an amplifier.
- Phono input (stereo RCA) with RIAA preamplifier.
- Wireless receiver input, aiming to work with a wide range of wireless microphones.
- Analogue channel-strip module with real analogue EQ and compression under digital control.

Audio outputs:
- XLR line output; balanced quarter-inch stereo output; unbalanced quarter-inch and eighth-inch output.
- Headphone output (quarter-inch and eighth-inch); headphone output with a configurable push-button knob for volume or other functions.
- Amplifier modules in several power levels (up to about 1000 W), built around off-the-shelf Class D amplifier boards, with swappable speaker output plates (quarter-inch, Speakon, screw terminals) and monitoring of temperature, clipping and load.
- Wireless output for in-ear monitors.
- Speaker management module with crossovers, EQ and limiting.

Other audio:
- RCA stereo I/O; S/PDIF coaxial and optical I/O; AES3 I/O; MIDI I/O.
- Bridges to Dante, AES50, AES67 and AVB/Milan.
- Intercom modules with TRRS headset jacks and basic controls for full-duplex intercom over the network.

Video:
- HDMI, SDI and DVI input and output; analogue video I/O (component, VGA, S-Video); HDBaseT input and output.
- Inside the network, video uses a light intra-frame production codec (tens to about 150 Mbps per stream). Streaming re-encodes once more to a distribution codec.
- Hardware video encoder module for streaming without upgrading the hub; simple media server module.

Lighting and show control:
- DMX input and output; Art-Net and sACN gateway; LTC and MIDI timecode I/O; GPIO module.

Control and automation:
- Relay module; motor control module for projector screens and blinds; RS-232 and IR control modules; solenoid and actuator driver.
- Control system interface modules that start a control-surface column from anywhere on the AV chain (control gateway).

Computer, USB and media:
- USB computer connection (see hubs), presenting chosen modules to a computer as a standard audio and video device.
- USB over the network: an input module captures a keyboard, mouse or touch screen, and an output module presents it to a distant computer. In the preferred version, the output module emulates the USB device itself (using a USB On-The-Go port and the Linux USB gadget framework), so no software is needed on the computer.
- USB host module: a small computer with a USB-A port that exposes any class-compliant USB audio or video device (for example a USB mixer or webcam) to the network.
- Ethernet connection module: joins the chain and offers an Ethernet port for a computer or other device.
- Media module with USB and SD slots to play audio, video and text files from removable media.
- Bluetooth module for audio in and out and for control devices such as keyboards and braille displays; several may be used to extend range.
- Arduino-compatible module so community members can run their own microcontroller programs on the system.
- Additional processing module to add effects and computing power.

Infrastructure:
- Power, auxiliary power, outlet and battery modules (section 4.5).
- Blank module: passes the chain through and provides structure; may contain a drawer or shelf.
- Switch module made with an off-the-shelf switch design in the module form factor.

### 4.9 Endpoint smart cables

- A small inline unit with an RJ45 jack on one end and an XLR, quarter-inch or headphone connector on the other, containing a preamplifier or headphone amplifier, converter, processor and PoE power.
- It connects to the microphone or instrument through a short replaceable jumper cable, avoiding strain on the instrument's connector. The release latch is recessed so the jumper is meant to stay attached.
- Versions: microphone input, instrument input (acting as an active DI), and personal in-ear monitor output with a volume control.
- Each smart cable identifies itself, so the software shows only connected sources, labels them by type and loads a suitable starting preset.
- Later versions include microphones with the electronics built in and an RJ45 connector, and partner products such as powered speakers with a native network input.

## 5. Hubs

The hub discovers and manages modules and provides routing, processing, configuration, and connections to wider networks and computers.

### 5.1 Hub tiers

- Tier 1, connection and bridge hubs: no processing beyond routing and level. They translate the system to other formats and can start a whole small system on their own or expose part of a larger one. The USB version appears to a computer as a standard class-compliant USB audio device (no driver needed), so a few modules form a desktop audio interface. Other versions bridge to Dante, AES50 and AVB.
- Tier 2, simple hub: about two modules wide, with modest processing for small systems. Not limited in features, only in processing power.
- Tier 3, large hub: six to eight modules wide, replacing a full DSP processor or high-channel-count mixer, with some video processing and a 10 Gigabit port.
- PCIe card: the hub software runs on any capable computer with an add-in card providing the network ports (including 10 and 40 Gigabit) and hardware timing, for as much processing as required.
- One common networking design is used in every hub tier and the PCIe card, so one driver serves all.
- Multiple hubs can work together.

### 5.2 Hub software

- Runs a real-time Linux operating system optimized for audio and video (similar in approach to Elk Audio OS).
- All audio processing is done with plugins (VST3, with LV2 and CLAP desirable), so users can run any plugins they choose, limited only by processing power. A good default set (EQ, compression, gate, reverb, delay) is included.
- Automatic gain setting, automatic microphone mixing, echo cancellation and noise reduction.
- Video processing such as colour balance, chroma key and overlays, and video switching and streaming built on existing open-source libraries (for example libobs from OBS Studio).
- A resource dashboard shows processor, memory and network load. Rather than the designer restricting features by tier, users may run anything their hardware can handle, including video conferencing (for example Zoom or Teams), recording and streaming.
- A view of all modules, their physical order and their status.
- Sending shell, Telnet, SSH and other commands to third-party equipment.
- A Matter bridge and Home Assistant integration for smart-home and building devices.
- Virtual sound card and camera drivers allow computers on the network to use system audio and video as ordinary devices.

## 6. The modular control surface

The control surface works on its own (for example as a DAW controller, computer accessibility device, streaming controller, lighting controller or appliance panel) or as the controls for the AV system.

### 6.1 Physical design

- Blocks are shallow metal units of a single uniform depth, sized on a grid of roughly 25 to 38 mm (about 1 to 1.5 inches). Blocks are whole multiples of the grid. Faders are one unit wide and several tall, and two fader blocks side by side give comfortable fader spacing.
- Blocks mount onto a metal backplate or frame for rigidity and alignment.
- Blocks form columns of up to about 16 blocks. Two columns can fit inside a standard two-gang wall box opening with a wider custom cover plate.
- Multi-wide blocks use one control board, and pass the neighbouring column's bus through behind them.

### 6.2 Carriers and inserts

- Carriers are smart blank blocks in standard sizes (for example 1x2, 1x3, 1x4, 2x2, 2x3, 3x3) containing one control board and several sockets.
- Inserts are simple parts that plug into the sockets: a knob, a button, a small display or a blank. Users rearrange them at any time, so an EQ strip can become a transport section.
- Fixed workhorse blocks (4 buttons, 4 knobs, 1 or 4 motorized faders) provide density, and specialty blocks provide single large controls such as a master knob, jog wheel or large talkback button.

### 6.3 Buttons with swappable tops

- A base button contains a quality mechanical switch, an RGB light and a standard stem.
- Tops press onto the stem: clear caps over printed labels; molded tactile shapes (dish, dome, bar, cross, arrows, raised numbers, textures, braille); soft rubber; various shapes and colours.
- The 3D model of the stem interface is published so anyone can design, 3D print or mold tops, and share them in a public library, including tops for individual accessibility needs.

### 6.4 Capacitive touch-to-identify

- Every knob, fader cap and button includes a capacitive touch sensor.
- Lightly touching a control announces its current function through a screen reader or speech, without changing anything.
- The same signal is used for: showing the touched parameter on a display, selecting a channel by touching any of its controls, stopping a motorized fader from moving while touched, previewing what a mute or solo will affect, and showing contextual help.

### 6.5 Connections and communication

- Within a column, blocks communicate over an RS-485 bus. The column controller (aggregator) polls each block in turn many times per second; blocks only transmit when asked, so there are no collisions.
- Discovery cascade: the aggregator enables only the first block through a dedicated line. That block reports its unique serial number and receives an address, then passes the enable signal to the next block. This prevents simultaneous announcements and records the physical order of blocks, which the software uses to draw the layout.
- Connectors: blocks use RJ45 connectors and ordinary Ethernet cables, matching the AV system so the whole platform uses one cable type. The eight conductors carry the RS-485 pair, low-voltage DC power, ground and the discovery line.
- Adjacent blocks may be joined by a short RJ45 bridge plug. In another version, adjacent blocks join directly through magnetic spring-pin (pogo) contacts on their sides carrying the same signals, while RJ45 is used wherever a cable is needed. In other versions, blocks use USB-C shaped connectors (not carrying USB) or contacts through the backplate.
- Safety rule: no power is applied and no bus driver is enabled on a connector until the block has confirmed it is connected to another device of this system, so accidentally connecting a block to a computer or network switch causes no harm.
- Column aggregators join each other using raw Layer 2 Ethernet frames (no IP stack needed), daisy-chained and optionally formed into a ring with RSTP. One aggregator may serve several columns.
- A control gateway module on the AV chain can start a control column anywhere along an AV cable run, keeping control and AV traffic logically separate.

### 6.6 Main controller and outputs

The main controller holds the mapping and translates control events to: USB MIDI, Mackie Control and HUI for DAWs; USB HID (keyboard, mouse, joystick) for computer and accessibility use; MIDI DIN; RS-232; IR; SSH and Telnet; OSC and AES70; Matter, Home Assistant and Homebridge; Bluetooth LE and Thread; and the AV hub. Small wall-box controllers handle one or two columns and connect by Ethernet or another output.

### 6.7 Control block catalogue

Buttons (single large, four small); knobs and encoders with LED rings; motorized faders; switches; OLED channel-strip displays; timecode and transport display; braille display block; joystick (two or three axes) for panning, moving lights and PTZ cameras; trackball; motorized knob; pressure-sensitive pad; infrared proximity sensor; A/B crossfader; touch strip; RS-232 output block; wireless transmitter block.

### 6.8 Displays

- Small channel displays run on the control bus.
- Larger displays are Android tablets running the hub's web interface in a locked-down app over Ethernet, gaining Android's built-in TalkBack screen reader. First certified off-the-shelf tablets with grid-sized mounts, later a purpose-built panel with no battery or camera and wired Ethernet.
- A premium tier of blocks surrounds controls with displays, puts screens behind buttons and on knob tops (for example showing camera previews on buttons or a plugin's own curve around knobs).
- Long-term direction: a surface that is simultaneously a visual display, a refreshable tactile and braille display and a haptic surface.

### 6.9 Wireless control

A wireless transmitter block for one or two columns, first using Bluetooth LE MIDI, later a custom low-latency 2.4 GHz link, with AES70 as the message format.

## 7. Mapping and configuration

- Every physical control is an input, every controllable parameter anywhere is an output, and logic sits between them.
- Built-in logic includes banks (a set of faders as a moving window over more channels), layers, modes (for example the right-hand faders switching from channels to bus masters), focus or spill (selecting a channel maps a detail area of knobs to it), groups and VCAs with members assigned live, and snapshots of the whole system.
- Three equal ways to configure the same underlying configuration:
  - a visual node editor (similar in spirit to the virtual console in QLC+);
  - a structured plain-text configuration file that can be read and edited line by line with a screen reader and is error-checked by the hub;
  - a guided wizard that asks plain questions and writes the configuration, needing no syntax knowledge.
- Optionally, a user describes the desired layout in plain language and the system proposes a configuration from the modules it has discovered.
- All configuration interfaces support full keyboard operation, screen readers, high contrast and resizable text.

## 8. Organization and values

- Run as a not-for-profit, pricing products at roughly 2 to 2.5 times manufacturing cost to cover staff, development, inventory, support and warranty.
- A trade-in program gives credit for returned modules toward new ones. Returned modules are tested, refurbished and resold at a discount with a warranty, lowering the cost of entry and reducing waste.
- Software and hardware are developed in the open with the community.

## 9. Development path

1. Accessible control surface prototype: microcontroller blocks on RS-485 over Ethernet cable, touch-to-identify with speech, and a USB connection appearing as MIDI and keyboard devices, with the plain-text configuration and wizard.
2. Desktop audio interface: a Tier 1 USB hub plus 2-in and 2-out modules using PTP and AES67-compatible network audio.
3. Larger hubs, more module types, video, bridges and the rest of the catalogue.

## 10. Licensing

This disclosure is published under Creative Commons Attribution-ShareAlike 4.0 (CC BY-SA 4.0). Software is licensed under GPL-3.0-or-later and hardware designs under CERN-OHL-S-2.0. See LICENSES.md in the project repository.
