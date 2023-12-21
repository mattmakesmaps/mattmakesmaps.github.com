---
title: Yaesu FTM-6000R Manually Program A Repeater
draft: false
description: Manually Program A Repeater on a Yaesu FTM-6000R Mobile Radio
date: 2023-12-20
tags: ham radio
---

# Manually Program a Repeater into the Yaesu FTM-6000R

This post documents how to manually program a repeater into the [Yaesu FTM-6000R](https://www.yaesu.com/indexVS.cfm?cmd=DisplayProducts&ProdCatID=249&encProdID=3BEE472B7584ACA7C6A71F41B8ADCEC9&DivisionID=65&isArchived=0) transceiver. The [basic](https://www.yaesu.com/downloadFile.cfm?FileID=17164&FileCatID=150&FileName=FTM%2D6000R%5FE%5FOM%5FENG%5FEH079M200%5F2110G%2DBS.pdf&FileContentType=application%2Fpdf) and [advanced](https://www.yaesu.com/downloadFile.cfm?FileID=17165&FileCatID=150&FileName=FTM%2D6000R%5FE%5FAM%5FENG%5F2110%2DA.pdf&FileContentType=application%2Fpdf) manuals don't clearly spell it out, so I'm hopeful this tutorial will fill that gap for myself and other owners of this rig.

**TL;DR:** Save the frequency in memory first. Then, from the newly created memory, set the memory to tone encoding (Menu 24 `SQL.TYPE`) and then set the tone frequency (Menu 25 `SQL.COD`). You'll only be modifying the settings for the individual memory slot.

## Walkthrough

The example repeater I'll be programming is WW7PSR for the 
[Puget Sound Repeater Group](https://web.psrg.org/) (PSGR).

- Frequency: 146.960MHz
- Offset: -0.6MHz
- CTCSS Tone: 103.5Hz

1) Power on the radio
2) Enter VFO mode by pressing the `V/M mw` button until the memory `M` button in the upper-left corner is not displayed.
3) Key in desired frequency using the mic.
4) Save the frequency to memory (The following procedure can be found from pg. 18 of the [manual](https://www.yaesu.com/downloadFile.cfm?FileID=17164&FileCatID=150&FileName=FTM%2D6000R%5FE%5FOM%5FENG%5FEH079M200%5F2110G%2DBS.pdf&FileContentType=application%2Fpdf)), `Writing to memory`). Note at this point we aren’t worrying about setting the tone.
    - Press and hold the `[V/M mw] key. The `M` icon in the upper-left hand corner should be flashing along with the three digit memory slot representing the lowest unused memory.
    - Rotate the dial to the desired memory slot, or leave it alone if you want to use the lowest unused memory slot.
    - Press and hold the `[V/M mw] key to save the memory.
5) The `M` icon should now be solid, and you should see the corresponding frequency and memory slot number.
6) To set the tone, press and hold the `F Menu` button and select menu `24: SQL.TYPE`.
7) Press the VFO to enter the `SQL.TYPE` menu, and rotate until you see `TON.ENC`. Press the VFO to confirm.
8) At this point, the memory channel is set to use a CTCSS tone, but we haven’t configured the tone frequency.
    - Using the `F Menu` button, dial in menu `25 SQL.COD`. Press the VFO to enter the menu.
    - Rotate the VFO to dial in the correct CTCSS Tone (in our case 103.5Hz) and press the VFO to confirm your selection.

The memory slot should now be updated to not only include the frequency and offset of interest, but also the tone frequency. Steps 6-8 above can be repeated on any memory slot to set the tone for that specific memory slot.

## Repeater Offset

In this example, the `-0.6MHz` offset was already configured automatically by the radio based on the frequency. If you need to change this, you can dial in the saved memory slot and use Menu `20 RPT.SET` to change the offset direction, and menu `21 RPT.OTR` to set the magnitude of the Repeater Shift.

## A note regarding menus.

The FTM-6000R has two layers of menus accessible from the `F Menu` button. A quick press drops you into a “favorites” subset. A press-and-hold gets you into the full menu set, but hides those that are already in the “favorites”. If you don’t see a menu item you’re looking for, try switching from the “favorites” menu to the full menu set, or vice-versa.
