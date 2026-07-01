---
layout: post
title: "Reading a Tamagotchi the Other Way — Chasing UART on the Test Points"
date: 2026-06-29
categories: [hardware, reverse-engineering]
tags: [tamagotchi, uart, serial, logic-analyser, sigrok, firmware, test-points, COB]
description: "When the microscope couldn't read the die, I went after the data electrically — soldering onto the board's test points and hunting for a serial interface that, it turns out, was never there."
---

This is the second half of the Tamagotchi project. In the [first post](#) I decapped the chip, exposed the die, and ran straight into a wall: my microscope can't resolve enough detail to read the silicon. The decap worked; the optical readout didn't.

So I changed tack. If I can't *read* the chip, maybe I can get the firmware out *electrically* — find a serial interface on the board, solder onto it, and listen. This is the story of that attempt, the research I leaned on, and why it ended where it did.

It's a failure write-up, mostly. But it's a useful one, because the *reason* it failed tells you something real about this hardware.

> **A note on what this board actually is.** I bought this advertised as a "Gen 2" Tamagotchi, but the board is marked `No: 42800VR5` and dated `2024-3-20` — so it's modern-production hardware in a classic-style shell. "Gen 2" here describes the gameplay generation and shell it imitates, not the silicon inside. I genuinely don't know the exact part under the epoxy: it could be running the classic ROM on a modern low-cost controller. What I *can* say for certain, from my own probing, is that it's a chip-on-board design with no accessible serial interface — and that's the part that matters for this post. Where I'd otherwise have asserted "it's a simple 90s ASIC," I'm instead leaving the generation an open question, because that's the honest position.

---

## The plan, and the research behind it

The dream was UART. A lot of embedded devices leave a debug serial line exposed on the PCB — test pads the factory used for programming or QA that still spew boot output if you connect to them. If the Tamagotchi had one of those, the chip would happily tell me what it was doing on power-up, and possibly let me dump flash.

Before touching the iron I read everything I could find. The cornerstone is Natalie Silvanovich's (natashenka) Tamagotchi work — the [29C3 talk](https://www.youtube.com/watch?v=c4PoacCB9GM) and the [Tamagotchi-Hack repo](https://github.com/natashenka/Tamagotchi-Hack). The thing that stuck with me reading her work and the follow-on community write-ups: classic Tamagotchi hardware is built as a closed, deliberately-minimal toy with no friendly debug door, which is exactly why the early ROM extraction was *hard*. That tradition of being locked-down by design held true for my board too, whatever the exact chip turned out to be. I went looking anyway, because the point was to read *my own* Tama, not download someone else's dump.

> **📷 Photo space — the board, test points labelled**
> *The back-of-board shot showing the test points. Worth annotating: TP1–TP15, BAT+, BAT-, GND, RESET, the two buzzer pads (BZ1, BZ2), and the board markings — `No: 42800VR5`, dated `2024-3-20`.*

---

## Mapping the board

The back of the board gives you a lot to work with on paper: a full row of test points (TP1 through TP15), clearly labelled `BAT+`, `BAT-`, `GND`, a `RESET` pad, and two buzzer pads (`BZ1`, `BZ2`). Promising-looking. The plan was simple:

1. Probe every test point with a multimeter to find power, ground, and any candidate signal lines.
2. Solder onto the candidates.
3. Listen — first passively with a logic analyser, then with a USB-to-UART adapter.

---

## Probing for signs of life

With batteries in and `BAT-` as my ground reference, I went down the test points one at a time looking for anything that behaved like a UART line — something sitting near rail at idle that pulses during boot.

Two pads gave me something: **two test points both reading ~2.2V**. On a 3V system, a pad pulled up to ~2.2V rather than full rail is *exactly* what an idle UART line looks like when there's a series resistor inline — and two of them reading the same value is what you'd expect from a TX/RX pair, both floating high at idle. That looked, on paper, like a found interface.

Everything else read a flat **0V**, including the labelled `BAT-` which confirmed my ground reference. So the working theory was: two 2.2V pads = TX and RX, `BAT-` = GND, go listen.

> **📷 Photo space — multimeter on the test points**
> *Probing shot, ideally showing the ~2.2V reading on one of the candidate pads.*

---

## Soldering on and listening

I tinned and soldered thin wire onto the two 2.2V pads and onto `BAT-` for ground. These pads are tiny and lift if you dwell on them, so it's a flux-and-tap job, under two seconds each.

The right order here is **logic analyser first, UART adapter second**. The analyser is passive — it just listens, can't damage anything — and it'll tell you both whether there's *any* activity and what the baud rate is by measuring the pulse widths. Only once you know there's real traffic do you swap to the UART adapter to actually talk.

My analyser is a Saleae Logic clone running `fx2lafw`, driven from `sigrok-cli`. The capture looked like this:

```bash
# raw capture — just confirm there's ANY activity on the two candidate pads
sigrok-cli -d fx2lafw --channels D5,D7 \
  --config samplerate=1m \
  -o capture.sr \
  --time 5s
```

Start the capture, then power-cycle the Tamagotchi so you catch the boot window. And to try decoding UART live across the usual baud rates:

```bash
sigrok-cli -d fx2lafw --channels D5,D7 \
  --config samplerate=1m \
  -P uart:rx=D5:tx=D7:baudrate=115200 \
  --time 5s
```

I ran it at 115200, then 57600, 38400, 19200, 9600. I swapped which pin was RX and which was TX. I re-ran the raw capture and opened it in PulseView to eyeball the channels directly.

**Nothing. Flat lines on every channel, every baud rate, every combination.** No pulses on power-up, no garbage, no traffic at all.

> **📷 Photo space — sigrok / PulseView capture**
> *A screenshot of the flat capture — the "nothing happened" evidence. The empty trace is the point.*

---

## Why it was never going to work

Empty captures narrow things down to a few possibilities — flaky solder joints, a signal too fast for the window — but after re-seating the wires and re-running, the honest conclusion is the simplest one:

**Those 2.2V pads were never a UART. They were voltage bleed from internal pull-ups, not an active serial line.**

Classic-style Tamagotchi hardware is built as a closed, deliberately-minimal toy. There's no reason for it to carry a hobbyist-accessible debug UART — Bandai gains nothing by exposing one. The test points scattered across the back of the board are almost certainly **bed-of-nails manufacturing test contacts** — pads the factory used for QA on the production line, not a debug or programming interface. Whether the silicon under the blob is a genuine vintage ASIC or a modern low-cost controller re-running the classic ROM, the result for me is the same: nothing to talk to. A reading of 2.2V on a multimeter is just a high-impedance pad floating; it looks identical to an idle UART line on a meter, which is exactly the trap I walked into.

That's the real lesson of this post. A multimeter can't tell you the difference between "idle serial line" and "pad pulled up to nowhere." Only the analyser can — and the analyser's verdict was *nothing*. The interface I thought I'd found didn't exist.

---

## So where does that leave it?

Both routes are now spent, in their current form:

- **Optically** (post 1): die exposed, but my microscope can't resolve enough to read it.
- **Electrically** (this post): no serial interface exists to read it through.

The genuinely-next options from here are more invasive — desoldering and directly reading a ROM/flash part, or getting the die under proper SEM-grade magnification — both of which need kit I don't currently have. There's also the boring shortcut I deliberately skipped: the community has already dumped Gen 1 and Gen 2 ROMs, so the data exists. But downloading someone else's dump was never the point. The point was to read *mine*.

There's a more promising future target sitting in plain sight, though. The newer WiFi Tamagotchi — the **Uni** — is built on an **ESP32-S3**, which is a documented, open platform with real community tooling and an actual debug UART. That's a device you can genuinely flash custom firmware onto, assuming Bandai didn't lock it down with flash encryption. The [TamaHacks wiki](https://tamahacks.com/index.php?title=Uni) is tracking exactly that. That's likely where this project goes next — from *reading* a Tamagotchi to *running my own code on one*.

---

## Resources

### Talks

- **[29C3 — Many Tamagotchis Were Harmed in the Making of this Presentation](https://www.youtube.com/watch?v=c4PoacCB9GM)** — Natalie Silvanovich (natashenka). The foundational Tamagotchi RE talk; covers the original serial/ROM extraction methodology and why this hardware fights you.

### Papers & write-ups

- **[natashenka/Tamagotchi-Hack](https://github.com/natashenka/Tamagotchi-Hack)** — GitHub. The original code from the 29C3 talk: ROM reading, patching, code execution.
- **[Hack those Tamagotchis!](https://natashenka.ca)** — natashenka.ca. Companion write-ups to the talk.
- **[TamaHacks Wiki — Uni page](https://tamahacks.com/index.php?title=Uni)** — community research on the ESP32-S3-based Uni; the right place to look before going after the WiFi model.
- **[UART, U-Boot, and USB](https://voidstarsec.com/blog/uart-uboot-and-usb)** — VoidStar Security. Solid general embedded-RE walkthrough for finding and using serial interfaces.
- **[Hardware Hacking 101 — UART](https://ivanorsolic.github.io/post/hardwarehacking1/)** — Ivan Orsolic. Practical UART hunting with an FT232H / Bus Pirate.

### Tools

- **[sigrok / PulseView](https://sigrok.org/)** — the logic analyser software used above (`sigrok-cli` + `fx2lafw` for Saleae Logic clones).
- **[esptool](https://docs.espressif.com/projects/esptool/en/latest/)** — Espressif's flash tool, for if/when the project moves to the ESP32-S3 Uni.
