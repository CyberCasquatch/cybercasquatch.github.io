---
layout: post
title: "Decapping a Tamagotchi — Extracting the Die from the Epoxy Blob"
date: 2026-06-29
categories: [hardware, reverse-engineering]
tags: [tamagotchi, decap, epoxy-blob, die-imaging, acid-decap, COB, microscopy]
description: "Burning and dissolving the epoxy off a chip-on-board package to expose the silicon die underneath — practice chips, a Gen 2 Tamagotchi, and the wall I hit at the microscope."
---

This is the first of two posts about a rabbit hole I went down trying to read the code on a Tamagotchi. I wanted to know how the thing actually works at the silicon level, and that meant getting eyes on the die. The chip you're after isn't a tidy little package you can desolder and drop into a reader — it's a **chip-on-board (COB)** blob, a bare die glued straight to the PCB and drowned in a glob of black epoxy. No package, no pins, no markings, no datasheet. Just a blob.

So before I could read anything, I had to get that epoxy off without wrecking the die underneath. This post is the physical side of the story. The [second post](#/_posts/Projects/2026-06-29-reading-a-tamagotchi-uart-test-points.md) covers the other route I tried — going after the data electrically through the board's test points.

A quick note on naming: this was sold to me as a "Gen 2" Tamagotchi, but the board is modern-production (marked `42800VR5`, dated 2024). "Gen 2" describes the gameplay style and shell it imitates, not the chip inside — so I'll just call it a chip-on-board Tamagotchi and leave the silicon's exact lineage as an open question.

---

## What we're looking at

A COB part is about the cheapest way to put a chip on a board. The silicon die is bonded directly to the PCB, wire-bonded to the pads, and then potted under epoxy to protect those hair-thin bond wires. It's everywhere in cheap consumer electronics for exactly that reason — it's cheap to make. The downside, if you're the one trying to reverse engineer it, is that there's nothing to read from the outside. No part number to look up. The ROM is baked into the silicon itself.

This is the same construction that made the early Tamagotchi ROM dumps such a big deal — there was no easy electrical path in, so people had to get physical.

> **📷 Photo space — IMG_0902 (front / component face)**
> *Drop the front-of-board shot here. The big central black blob is the COB. Worth annotating with arrows: COB blob, LCD ribbon connectors (top + bottom), the CANCEL / OK / SELECT button pads, and the SMD passives (C8, C9, C10, R2, R3, R6, JP1) clustered around it.*

![Photo of Tamagotchi board](/assets/images/Tamagotchi/BackofTama.png)

---

## Equipment and PPE

Primary setup outside because of the nitric acid.

![Photo of setup1](/assets/images/Tamagotchi/Setup1.png)

> **📷 Photo space — bench / gear shot**
> *A shot of the setup: fume extraction, acid, microscope, the prepped practice chips lined up.*

Quick word before anyone copies this: the chemistry here is dangerous, not internet-dangerous. Fuming nitric acid does not forgive mistakes. None of this is a how-to — it's a write-up of what I did. If you don't already know how to handle these reagents safely, this is not where you learn.

What I used:

- PPE: acid-rated gloves, goggles/face protection, and proper ventilation. This is the non-negotiable part.
- A Dremel for mechanical prep.
- Acid for the chemical decap stage.
- A microscope plus a phone-to-eyepiece adapter for imaging.
- A pile of junk chips — dead boards I didn't care about — as practice before I touched anything I wanted to keep.

---

## Step 1 — Mechanical prep (the Dremel stage)

The point of dremelling first is to remove the bulk of the epoxy mechanically so the acid has far less material to chew through. Less acid, less time, less heat, less collateral damage to the surrounding passives.

The whole game here is restraint. You're trying to thin the blob, not breach it. Go too far and you crack or score the die, or shear the bond wires, and the part is dead before chemistry ever starts. Heat is the other enemy — the epoxy is formulated to survive soldering temps, so grinding generates heat that can cook the die if you lean on it. Short passes, let it cool.

After dremelling some of the tests.

![Photo of setup2](/assets/images/Tamagotchi/AfterDremel1.png)

After dremelliing the first tamagotchi.

![Photo of setup3](/assets/images/Tamagotchi/AfterDremelTama1.png)


What they look like after being dremelled but before the acid.

![Photo of setup4](/assets/images/Tamagotchi/PhotosBeforeAcids/Scale2.jpg)

> **📷 Photo space — practice chips, dremelled and ready**
> *The junk chips with the bulk epoxy ground down, before the acid stage.*

---

## Step 2 — Chemical extraction (burning the epoxy)

This is the stage everyone pictures when they hear "decapping" — the epoxy being chemically eaten away to leave the bare silicon sitting there exposed.

> **📷 Photo space — the burn / acid stage**
> *Reaction shots go here: epoxy reacting, the blob breaking down, the residue. Whatever you captured of the actual chemistry — this is the money section visually.*
>
> assets/images/Tamagotchi/HeatingFakeTama.png
> assets/images/Tamagotchi/Acid3.png
> *If you have a before/during/after sequence, run them in order here.*

> **📷 Photo space — exposed die, first look**
> *The first clean shot of the die once the epoxy was gone, before it went under the scope.*

![Photo of acid](/assets/images/Tamagotchi/TamasAfterAcid.png)
> 
---

## Step 3 — Imaging the die, and where I hit the wall

This is the part I am currently unable to get past.

I got the epoxy off. I exposed the die. On the practice chips and on the Tamagotchi, the chemistry worked — there was bare silicon sitting there to look at. The decap was a success.

The microscope was the failure.

What I have right now simply doesn't resolve enough detail. You can *see* the die — the rough floor plan, the large structures, the general layout of regions on the silicon. What you can't do at this magnification is read anything meaningful: no tracing of individual gates, no following the metal layers, nothing close to the resolution you'd need to actually reconstruct logic or pull a ROM optically. To do that properly you're into high-magnification optical work or, realistically, a scanning electron microscope — and an SEM is lab-level access I don't have on the bench.

> **📷 Photo space — die under the microscope**
> *Your die-imaging attempts go here. Even the ones that came out too blurry to read are worth including — they make the resolution-wall point concretely. A side-by-side of "what I can see" vs "what I'd need to see" would land well.*

So the result is a split decision: **decap, successful. Optical readout, blocked on magnification.** The die is sitting there exposed and I can't read it. That's not a chemistry problem any more — it's purely a microscope problem, which is, at least, a solvable one with better glass or borrowed SEM time. Worth chasing down whether a local hackerspace or university EM facility will give external access.

---

## Where this goes next

Hitting the optical wall is what pushed me to the other approach: if I can't *read* the silicon, can I get the data out *electrically*? That's the whole of the second post — soldering onto the board's test points and trying to coax firmware out over a serial line. Spoiler: that didn't go the way I hoped either, but for completely different reasons.

---

## Resources

### Talks

- **[29C3 — Many Tamagotchis Were Harmed in the Making of this Presentation](https://www.youtube.com/watch?v=c4PoacCB9GM)** — Natalie Silvanovich (natashenka), CCC 2012. The foundational Tamagotchi reverse-engineering talk; covers why physical access mattered so much on this hardware.

### Papers & write-ups

- **[First Glimpse into the Soul of a Tamagotchi](https://kwartzlab.ca/2013/05/first-glimpse-into-the-soul-of-a-tamagotchi/)** — KwartzLab Makerspace. Early die-level investigation of Tamagotchi hardware.
- **[Tamagotchi ROM Dump and Reverse Engineering](https://hackaday.com/2013/06/10/tamagotchi-rom-dump-and-reverse-engineering/)** — Hackaday. Covers the full arc from physical access through to ROM extraction.
- **[Hacking the PIC 18F1320](https://bunniestudios.com/blog/?p=73)** — bunnie's blog. Context on reading protected microcontroller memory and why physical access is sometimes the only path in.
- **[Removing / Decapping an Epoxy Blob](https://www.eevblog.com/forum/beginners/removingdecapping-an-epoxy-blob/)** — EEVblog forum. Community methods specifically for COB / glob-top removal.
- **[iPhone-to-Microscope Eyepiece Adapter](https://www.thingiverse.com/)** — DemuxLabs, Thingiverse. The phone-to-eyepiece adapter approach for die imaging. *(swap in your exact thing: link)*
