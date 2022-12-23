---
layout: post
title:  "Input Eastern Languages With Steam Deck"
date:   2022-12-23 10:55:00 +0800
tag: steam deck linux ime fcitx
---

## Prerequisite

# Understanding of [Unix Philosophy](https://en.wikipedia.org/wiki/Unix_philosophy)

> 1. Make each program do one thing well.
> 2. Expect the output of every program to become the input to another, as yet unknown, program.

Which turns out to the fact that

1. the SteamOS keyboard doesn't count for input frameworks.
2. the input framework doesn't care about where the input comes from.
3. the applications don't care about what input framework is chosen, as long as they are properly registered.
4. the desktop cares no integration of input frameworks, they should care about integration to desktop on their own.

# Steam Deck Shortcuts

1. button "X" calls out the SteamOS keyboard on Desktop mode.
2. The keyboard dropped to **oppsite half of screen** of where **the cursor** lies.

## Flatpak Package required

> Make good use of the "Search" function

1. Open `Discover`
2. Search for "`fcitx5`", install it
3. Install additional input methods
    - Chewing for Taiwanese users
    - Anthy or Mozc (suggested) for Japanese users
    - Hangul for Korean users

## Desktop Integration

1. Move your cursor to the bottom panel
2. Click `L2`, choose "Add Widgets"
3. Search for "Input Method" with a pen on the icon
4. Move your cursor on to it, and double click the right pad

## Autostart Fcitx on Login to Desktop Mode

# Copy `.desktop` from flatpak exported directory

1. Open Dolphin
2. Click on the `> Home` above main area
3. It now turns into input area
4. Go to `/home/.steamos/offload/var/lib/flatpak/exports/share/applications`
5. Move your Cursor onto `org.fcitx.Fcitx5.desktop`
6. Click `L2` and select Copy
7. Edit the location again to `~/.config/autostart`
8. Click `L2` on empty area, select Paste

```
Or, one liner in Konsole
---
$ cp /home/.steamos/offload/var/lib/flatpak/exports/share/applications/org.fcitx.Fcitx5.desktop ~/.config/autostart/
```

# Safeguard Fcitx over IBus

- Create a file:

```
~/.config/plasma-workspace/env/input.sh
---
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
export XIM=fcitx
```


## Demo

<video width="100%" src="/assets/2022-12-23.webm"></video>

## Technical Details

# Side Notes

- The Fcitx5 flatpak has fcitx-qt input module plugin included.
- Even though the flatpak doesn't have gtk immodule, it works under flatpak firefox anyway.
- Users got to used to the scenario to change the input method by cursor then change the keyboard layout by controller.
- Noted that `libchewing` is developed for general keyboard, thus the candidate number selection is based on general keyboard, instead of indication of the Steam keyboard.
- You can edit the preference of Chewing module to use `asdfghjkl;` as selector instead.

# Valve package details

- interesting finding from `steamdeck-kde-presets`:

    - `export MOZ_ENABLE_WAYLAND=1` in `/etc/profile.d/kde.sh`
    - Input method modules are exported in `/etc/xdg/plasma-workspace/env/ibus.sh`, thus we kept our own in `~/.config/plasma-workspace`, the coresponded per-user configuration directory, as shown in above tutorial.

- Noted that throughout this tutorial, we didn't disable the said **readonly** status quo.
