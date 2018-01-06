---
title: "Ergodox Keymap Compatibility"
date: 2018-01-06T23:19:58+09:00
---

I have two keyboards: Ergodox EZ and Infinity Ergodox.
These keyboards use same firmware, but require different build options.
This is the methodology to share keymap.c file between two keyboards compiler option.

# Ergodox EZ
## Create keymap.c and firmware at official configurator
[http://configure.ergodox-ez.com/keyboard_layouts/new](http://configure.ergodox-ez.com/keyboard_layouts/new)

## Write keymap.c to Ergodox EZ
You can use [teensy loader](https://www.pjrc.com/teensy/loader.html).

## Commit keymap.c to your keymap repository
like [this commit](https://github.com/yuukigoodman/qmk_firmware/commit/aca0caa1dd2df0d7c0d18ab645f0b859edbd35b2)

# left hand Ergodox Infinity
## Fix differences
- replace from `#include "ergodox_ez.h"` to `#include QMK_KEYBOARD_H` at line 1
- add `#ifdef RGBLIGHT_ENABLE` and `#endif` around `rgblight_mode(1);` at line 72
  - see also: [qmk/qmk_firmware#1080](https://github.com/qmk/qmk_firmware/issues/1080)

## Try build and write firmware, but writing cause errors

```bash
$ make ergodox_infinity:KEYMAP:dfu-util
QMK Firmware preonic-1.0
Making ergodox_infinity with keymap KEYMAP and target dfu-util
...
dfu-util: Invalid DFU suffix signature
dfu-util: A valid DFU suffix will be required in a future dfu-util release!!!
dfu-util: More than one DFU capable USB device found! Try '--list' and specify the serial number or disconnect all but one device

make[1]: *** [dfu-util] Error 74
make: *** [ergodox_infinity:KEYMAP:dfu-util] Error 1
Make finished with errors
```

## Workaround

```bash
# set Infinity Ergodox to write mode, then type this command
$ dfu-util --list
dfu-util 0.9

Copyright 2005-2009 Weston Schmidt, Harald Welte and OpenMoko Inc.
Copyright 2010-2016 Tormod Volden and Stefan Schmidt
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Found Runtime: [05ac:8290] ver=0153, devnum=4, cfg=1, intf=5, path="20-3", alt=0, name="UNKNOWN", serial="UNKNOWN"
Found DFU: [1c11:b007] ver=0000, devnum=18, cfg=1, intf=0, path="20-2", alt=0, name="Kiibohd DFU", serial="mk20dx256vlh7"

# then, try writing again
$ dfu-util --device 1c11:b007 -D .build/ergodox_infinity_KEYMAP.bin
```

# right hand Ergodox Infinity
DON'T FORGET: MASTER=right option

```bash
$ make ergodox_infinity:KEYMAP:dfu-util MASTER=right
$ dfu-util --list
$ dfu-util --device 1c11:b007 -D .build/ergodox_infinity_KEYMAP.bin MASTER=right
```