---
layout: post
title:  "Logi Pop Keys and Mouse"
date:   2021-12-11 13:45:00 +0800
tag: logi logitech pop keys keyboard mouse
---

The [Logitech Pop Keys and Mouse](https://www.logitech.com/en-us/products/keyboards/pop-keys-wireless-mechanical.html) was released at the end of Sep, 2021 and was put into set as "Gift for Christmas Day" on e-shopping site here in Taiwan.

...And, yes, I bought a set...

Where I didn't notice that the emoji icons on the right edge cannot be recognized under Linux (at least, Arch Linux™️ )

## Troubleshoot Corner

### Pairing the mouse with Logi Bolt Receiver

1. install `solaar` (current version 1.1.0-1 in official repo supports it)

2. plug in the usb dongle

3. put the mouse into pair mode (long press the switch button until the light blinks)

4. press "pair new device" on solaar GUI, and it should discover the device for you, just follow the instructions.

### Keyboard mapping

1. Fn reversed, aka. it is default on instead of normal F1~F12

    if you don't like that behavior, just unlock it from solaar, and switch off.

2. Snapshot function (Fn+F5): it is default to send composite keystrokes of Windows shortcut, aka. `Meta(Suprt_L)+Shift+S`, just map _that_ sequence to your keyboard shortcut utility of desktop environment.

3. Wordprocessing buttons missing:

    There is no indication of `Home`, `Pg Up/Down`, `End` buttons, and the emoji shortcuts are unknown to linux

    But it is indeed compiled into shortcut of:

    - Fn+←: Home

    - Fn+→: End

    - Fn+↑: Page up

    - Fn+↓: Page down

4. misc:

    - F4: minimize all to show desktop, is mapped to `Meta(Super_L)+D`, which can then configured in shortcut utility.

    - F6: mute microphone, is not implemented

    - AI assistant(?) button near delete button: not implemented.

    - Battery level monitor: put `solaar` to tray

    example: put `solaar -w hide -b solaar` to ~/.bashrc
