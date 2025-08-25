---
title: "Desert Ears: My Off-Grid DIY Radio Observatory in Southeast Arizona"
description: "How I am turning a tiny, remote 1.25-acre plot in the Arizona desert into a solar-powered radio observatory with a hydrogen-line dish, HF and VHF/UHF antennas, Starlink backhaul, live webcams, and a buried micro-bunker for gear I can run and monitor from anywhere."
pubDate: 2025-08-25
tags: ["radio astronomy", "amateur radio", "hydrogen line", "SDR", "off grid", "solar", "Arizona", "HF", "VHF", "UHF", "DIY"]
---

## Why this exists at all

I own a weird little postage stamp of land in the remote southeast Arizona desert. It is about 1.25 acres. No neighbors, just farmland and sky. The county will happily sell me a stack of permits if I try to build anything that looks like a house, which is not my goal. I do not want a house. I want ears. I want my own direct tap into the radio universe. I want to listen for the cold whisper of neutral hydrogen, watch the Sun in RF, play with pulsar-grade workflows someday, and keep a quiet HF station idling in the background for when the bands open like a curtain. I want a small site I can run off the sun, leave alone for weeks, and steer from anywhere.

So I am building a tiny, off-grid radio observatory. It is fully DIY and affordable-ish. It has one job: be steady and quiet. That means a small solar array, careful power budgeting, cameras to livestream the site, Starlink for uplink and control, and as much equipment as possible tucked into a shallow underground “bunker” to keep heat swings and dust under control. Antennas above, brains below.

Arizona is perfect for this. Dry air, big skies, and lots of sun. The RF noise floor is way better in farm country than in a city. Peak sun hours in Arizona are high, which means the solar math actually works. The land is flat, there is nothing tall nearby, and I can set the geometry to match the sky I care about. Good enough.

## What the station needs to do

Checklist in human words:

- Run completely off grid on a small solar system with enough battery to survive nights and a couple of bad days.  
- Provide a reliable uplink and downlink through Starlink so I can control everything over the internet.  
- Host one small radio astronomy dish for neutral hydrogen work at 1420 MHz to start, fed into a clean LNA chain and an SDR I can script and schedule.  
- Host a quiet HF station made of multiple inverted-V dipoles, plus a couple of compact yagis for VHF and UHF experiments.  
- Put the computers, routers, charge controllers, and SDRs inside a weather-proof, critter-proof, ventilated micro-bunker set below grade.  
- Put a few low-power PoE webcams on poles to stream the site in real time.  
- Make everything serviceable and cheap enough that a mistake does not kill the project.

That is the brief.

## Site concept in plain English

Think of a small rectangle of desert with four zones:

1) **Power corner**. A ground rack with the solar combiner, a short run up to panels on a low fence or short pole framework, a battery box, and a small weather head for grounding.  
2) **Bunker**. A polymer concrete or FRP telecom vault set a little below grade with a gasketed lid. Inside it is a NEMA 4X electronics box on a backplate with the charge controller, DC distribution, an inverter, the network gear, SDRs, and the Raspberry Pi.  
3) **Antenna line**. A short line of masts: one for the small parabolic dish, one center mast for the inverted-V fan dipoles, and one for a VHF/UHF yagi stack. Conduits bring coax and control lines back to the bunker.  
4) **Perimeter cameras**. Two or three PoE cameras on 10 to 12 foot posts, looking inward, with one camera pointed at the sky and dish.

Everything is tied to a single point ground, with bonding jumpers between masts, the Starlink mast, the bunker ground bar, and the DC negative bus. Lightning is a desert reality. I am not trying to take a direct hit. I am trying to keep induced events from making the gear sad.

## Power budget approximations

This is the off-grid backbone. If this is wrong, everything above it is a paper kite. Here is the baseline continuous draw I am planning for when the site is “on” and doing normal work:

- Starlink Standard dish and router: average 75 to 100 W in active use.  
- Raspberry Pi 5 control host: about 3 to 8 W at idle to light load, with peaks above that when doing heavy I/O.  
- SDR receiver chain: 2 to 4 W for the dongle itself, plus a few watts for a bias-tee powered LNA if I run it that way.  
- Two PoE webcams: about 3 to 6 W each in daylight, up to 10 to 12 W with IR at night.  
- Network switch and PoE overhead: about 5 to 10 W.  
- Housekeeping overhead, sensors, fans: 3 to 5 W averaged.

Round up and be honest about losses and night duty cycles. My steady draw with two cameras running will hover around 100 to 140 W. Nights tick higher because camera IR turns on. Some daytime radio jobs push CPU and SDR bandwidth up a little. Average 120 W is a fair planning number. That is 2.88 kWh per day. Winter margin and bad weather days matter, so I size more like 3.5 to 4.0 kWh per day. Arizona is friendly to solar, which makes this sane.

**Array size**. With roughly 6.5 peak sun hours on average in Arizona over a year, a 700 to 900 W array will make the daily energy in good weather. That is without ugly derates. Add controller and heat losses and aim bigger. I plan to land around a 1.2 kW array. That gives me margin for winter, panels running hot, and one dead camera or a future third camera.

**Battery**. I want one full night and part of a second at the average draw. Call it 4 to 6 kWh usable. That is a 24 V 280 Ah LiFePO4 pack, or a 48 V 120 Ah pack, or equivalent in rack modules. I am going with LiFePO4 because it is safe, likes lots of cycles, and will not make me cry in year three. A 48 V bus is nice for lower current and smaller copper, but 24 V is simpler with common DC gear. I can step down to 12 V for the odd load. I will likely keep the Starlink on its stock PSU via the inverter at first for reliability, then explore DC direct tricks later if the numbers demand it.

## The micro-bunker

I am burying the brains in a shallow vault. A polymer concrete or FRP handhole in the 24 x 24 x 24 inch class is perfect, with a gasketed lid. Inside I mount a NEMA 4X enclosure on Unistrut or a backboard. The box holds a DIN rail and a metal backplate with the MPPT, DC distribution, fusing, inverter, a small PoE switch, the SDRs, and the Pi. I want one simple service loop for coax and one for Ethernet, both through proper glands. A filtered vent plus a low CFM DC fan across a desiccant pack keeps the humidity low and evens the temperature swings. The desert gets hot. Buried gear gets warm slowly then cold slowly. That stability is the point. The lid gets shade.

## Networking and control

Starlink goes up a short mast on the perimeter. Ethernet comes down to the bunker switch. The Pi 5 runs the station. I will put everything in containers so I can script long runs and keep services neat:

- A radio astronomy stack that can run scheduled 1420 MHz drift scans, average and store spectra, push data to S3, and alert me when something looks off.  
- A streaming stack that grabs RTSP from the cameras and relays a live stream to a public page and a private dashboard.  
- A network service that reverse tunnels home and gives me SSH even if Starlink changes the CGNAT flavor.  
- A small metrics stack for power, temperature, humidity, and device status.

If something locks up, a smart DC switch can hard power cycle individual loads so I do not have to drive out for a reboot.

## The 1420 MHz dish and front end

I am starting with neutral hydrogen at 1420.4058 MHz. It is the friendliest target in radio astronomy for a small station. There are two simple paths:

- **Dish plus feed**. A 1.2 m class offset dish with a 1420 MHz feed. The feed can be a simple waveguide horn, a patch, or a helical. For a first build I like a small waveguide horn or a commercial 1420 feed that bolts onto the dish arm. I drift scan, so I do not need tracking motors to begin. I fix the azimuth and adjust elevation seasonally.  
- **Horn only**. A big horn on a mast without a dish. This is lighter and easier, and it will still map hydrogen and track the Sun. The gain is lower than a dish, but the noise figure battle is the same and the system is simpler.

Either way, the RF chain is the same idea. Put a very low noise amplifier right at the feed with a bandpass that rejects the junk. The clean, cheap way to do that at 1420 is a filtered LNA module mounted in a small weatherproof box right behind the feed. From there a short length of low-loss coax runs down the arm to the mast, then into the bunker, where I can add another LNA or filter stage if needed and drop into the SDR.

On the bench I will check the whole chain for stability, compression, and spurs. In the field I will validate with Sun transits and a bright galactic target. Once it is steady, I run drift scans every clear day, average in software, and start building plots I can post.

## The SDR choices I actually like

I want two classes of receivers on site:

- **Hydrogen line and L-band work**. A better-than-RTL SDR with clean 12 to 14-bit performance is nice to have for dynamic range and averaging. SDRplay RSPdx or similar is a good middle ground and brings some front-end flexibility. Airspy also works well in this role and has solid software support. I can still do first light with an RTL-SDR if I am smart about gain and filtering.  
- **General scanning and utility jobs**. An RTL-SDR Blog V4 is cheap and honest. It has a software-switchable bias-tee for powering mast LNAs, and it is fine for HF to L-band experiments if I am not silly about expectations. It is the duct tape of receivers.

The first LNA absolutely belongs at the feed. For 1420 I like filtered LNAs built for the hydrogen line. Put that right at the focus so the feedline loss happens after you have already bought down the system noise temperature. If I need more gain to get the spectrum sitting in the sweet spot of the SDR ADC, I can add a second LNA in the bunker ahead of the receiver. I will not be greedy. More gain than necessary just invites trouble.

## The HF and VHF side quests

I want a simple HF presence for fun and for ionospheric sanity checks. I will put up a fan of inverted-V dipoles from a single center mast. I will start with 40 m and 20 m, maybe a 10 m wire on the same anchor since Arizona sun and 10 m are friends at the right time of day. The inverted-V format keeps my footprint small and only needs one tall support. I will space the wires so they do not tangle or couple badly and trim each leg to land the SWR dips where I actually operate. If I want to be extra lazy, I can add a remote tuner at the base and run a single wire and a counterpoise, but for the cost of a couple more insulators a fan dipole is cleaner.

For VHF and UHF I will mount a light 2 m and 70 cm yagi. This is for local beacons, satellite downlink experiments, meteor scatter tests, and the odd EME daydream. A small TV-class rotator is enough. I am not trying to chase LEO satellites yet. This is mostly for stable signals I can schedule and record.

## Cameras and the public window

I want the site to be visible. Two or three PoE cameras are enough. Daytime they sip just a few watts. Nighttime they kick on IR and pull a bit more. I will stream a composite view to a public page and keep single camera RTSP feeds for the private dashboard. Starlink makes this easy because I am always pushing outbound. I do not need inbound port forwards. I can do a clean RTMP relay to a streaming service and call it done.

## Safety, grounding, and things that bite

Everything bonds to a single point ground. Each mast or pole gets a ground rod and a heavy copper bond back to the station ground bar. The DC negative bus bonds there as well. Coax shields bond where they enter the bunker, and I insert lightning protectors where the coax transitions inside. I run all outside cables in conduit, seal the top ends, and put weep holes down low so condensation drains. The bunker lid gets lock bolts and the cameras watch the entire site.

## What this remote observatory is for

The romantic answer is that I want my own window into the invisible universe. The practical answer is that I want a steady stream of RF data I can control and trust. Hydrogen maps will start as simple drift scans with averaged spectra. Over time I will add routines that fold and subtract, then produce skymaps as I let the Earth spin under a fixed beam. The Sun is a daily metronome at these frequencies and gives me an honest signal to validate the chain. Initially, I am trying to get good at the craft of low-power, low-noise radio work with parts I can fix. Ultimately there might be a bit of "listening for aliens".

Arizona helps. Lots of sky, low humidity, and strong sun for the panels. The land is cheap, empty, and quiet. I can set this up without noise walls of routers and LED billboards. It is a better lab than my roof in a city.

## Bill of materials

Starting list with round numbers. Prices bounce, but the scale is right.

- **Solar**  
  - 6 x 200 W panels and a ground frame  
  - MPPT charge controller sized for array and battery voltage  
  - LiFePO4 battery bank about 5 kWh usable  
  - 1 small pure sine inverter 300 to 500 W continuous for Starlink PSU and odds and ends  
  - DC distribution, breakers, fuses, disconnects, lightning arrestor for the array  
- **Network and compute**  
  - Starlink kit on a short mast  
  - Raspberry Pi 5 with NVMe adapter and a rugged SSD  
  - 5 to 8 port PoE switch, low power, fanless  
  - Smart DC power switch or PDU for remote power cycles  
- **Radio astronomy**  
  - 1.2 m class offset dish with a 1420 MHz feed  
  - 1420 MHz filtered LNA at the feed in a weatherproof pod  
  - Airspy or SDRplay for the hydrogen line work  
  - RTL-SDR Blog V4 as a utility receiver and bias-tee source  
  - Short run of low-loss coax from feed to mast, then quality coax into the bunker  
  - Mounted enclosure for the mast electronics if needed  
- **Ham antennas**  
  - Center mast for inverted-V fan dipoles with three or four wire sets and insulators  
  - Lightweight yagi for 2 m and 70 cm on a small rotator  
  - Chokes, strain reliefs, center insulator, proper hardware  
- **Bunker and physical plant**  
  - 24 x 24 x 24 inch polymer concrete or FRP handhole vault with gasketed lid  
  - Interior NEMA 4X enclosure with backplate and DIN rail  
  - Cable glands, conduits, trays, desiccant, and a small DC fan  
  - Ground rods, clamps, copper strap or wire for bonding  
  - Coaxial lightning protectors and ground bar at entry  
- **Cameras**  
  - Two or three PoE bullet or turret cameras with IP66 or better ratings  
  - Short sun shades over each

If I reuse hardware, buy panels used, and shop sanely, this stays in reachable territory. The trick is not the parts. The trick is the plan and the discipline to keep it simple.

## Power math

Here is my first pass at the numbers I am using to size the system. They are conservative on purpose.

- Starlink average 75 to 100 W. I will plan at 85 W.  
- Pi 5 at idle around 3 W, with real use 5 to 8 W. I will plan at 6 W.  
- SDR active about 3 W. LNA at the feed powered by bias-tee adds a few watts at the Pi or SDR. I will call the pair 5 W.  
- Two PoE cams 6 W each by day, up to 12 W at night with IR. I will plan at 18 W daytime, 24 W nighttime.  
- PoE switch and small PDU overhead 8 W.  
- Misc sensors and fans 4 W.

**Daytime average** then is roughly 85 + 6 + 5 + 18 + 8 + 4 = 126 W.  
**Night average** bumps to around 85 + 6 + 5 + 24 + 8 + 4 = 132 W.

Daily energy near 3.1 kWh. Panels at 1.2 kW will make that even with losses most of the year in Arizona. The battery at 5 kWh usable gives a comfortable night plus some gray hours. If I add a third camera, the math barely moves.

## How I will operate it from afar

On the Pi I will keep a small schedule that does this:

- Sunrise check: log power, temps, and a quick broadband RF baseline to watch for new local noise.  
- Midday hydrogen scan: run a drift scan at fixed elevation, average into a spectrum, save raw and averaged data, and plot a daily.  
- Afternoon solar calibration: short Sun pass to validate gain and pointing bias.  
- Night camera mode: swap camera profiles to night and lower bit rate to save power.  
- Night RF: run HF monitoring windows when the bands are honest, and VHF beacon checks.  
- Weekly: run a higher resolution series for a longer average and back that up off-site.

Everything pushes data outward. Nothing requires me to get inbound to the site. I can do remote admin via a reverse tunnel and keep control if Starlink changes addressing or blocks inbound flows.

## The plan, step by step

**Phase 0: Bench first**  
- Assemble the hydrogen front end on the bench and validate the LNA chain into a dummy load and then an antenna.  
- Bring up the SDR software, pick the toolchain for spectra, and get scripts in place.  
- Do a living room power audit of the Pi, SDR, and a camera on a DC supply so I know the truth.

**Phase 1: Power and network**  
- Set the ground rack and plant the array.  
- Land the battery, MPPT, DC distribution, and inverter in a temporary above-grade box.  
- Mount Starlink on a short mast and get a clean connection.  
- Run the bunker conduits and bond the ground system.

**Phase 2: Bunker and cameras**  
- Set the handhole, backfill, and mount the NEMA box inside.  
- Move power gear into the bunker, clean up wiring, and test the fan and vent scheme.  
- Install the PoE switch, Pi, and smart DC switch.  
- Bring up two cameras and finalize the streaming stack.

**Phase 3: Hydrogen line first light**  
- Mount the dish or horn.  
- Install the filtered LNA at the feed and route a short coax run to the mast and into the bunker.  
- Land the SDR and bias-tee power.  
- Do a Sun transit and a couple of drift scans. Save plots. Fix anything dumb.

**Phase 4: HF and VHF**  
- Raise the center mast and install the inverted-V fan dipoles.  
- Build or install chokes at the feed point and at the shack entrance.  
- Put a small 2 m and 70 cm yagi on a TV rotator and give it a beacon plan.  
- Do a quick HF sanity check and set band schedules.

**Phase 5: Automation and science**  
- Add scheduled hydrogen scans, a weekly longer run, and simple skymap building over time.  
- Add alerts for power, temps, and network issues.  
- Publish the site page and keep a running log of changes and data drops.  
- Make it boring. Boring is the goal.

## What success looks like

Nothing fancy. It is a week of unattended runs with clean power curves, cameras that do not drop, and spectra that average into honest bumps where the sky says they should be. It is a daily log that is dull to read. It is a simple page where anyone can see the desert wind in a camera and the hydrogen wiggle in a plot. It is a system I can explain to a friend in ten minutes and hand off for a weekend without fear.

There is a lot I can grow into from here. I can scale the dish. I can add more battery. I can add a second horn and do cross-checks. I can experiment with polarizations, look at OH lines, or play with pulsar back ends. None of that matters if the basics are not boring first. So I am building a boring observatory to start.

## Notes to my future self

- Do not “optimize” the LNA chain until you have a month of stable data. A second LNA is a tool, not a goal.  
- Keep the cameras fewer and smarter. Power is precious at night.  
- If the site ever feels fragile, stop and harden the bunker.  
- Back up the Pi. Put the configs in git. Keep the images handy.  
- Write the runbook. Being able to tell someone else how to bring it up after a storm will be super helpful.

I have wanted to do this since I first learned the hydrogen line was a thing a human could measure with junkyard gear and patience. I finally have a patch of dirt where I can make it quiet. Time to build the ears and listen.
