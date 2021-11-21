# GA401I Arch Setup

This is a note to self in case I need to set up Arch on another machine. This isn't meant to be a guide.

As far as I remember,  I:
1. followed the first 2 videos with the installation guide from Arch to complete the installation. The videos demonstrate the steps from the installation guide with a few useful extra setup, such as setting up `sudo`.
2. setup `dwm`, `st`, `dmenu` following the 3rd video. 
3. followed through the Arch Setup Guide from ASUS Linux. 
4. setup `slstatus`.

## Recourses

### Youtube

1. [Arch Linux Installation Guide 2020](https://www.youtube.com/watch?v=PQgyW10xD8s)
2. [Arch Linux Install and Dual Boot with Windows 10 (UEFI) | Step by Step w/ Networking | 2021 Tutorial](https://www.youtube.com/watch?v=LGhifbn6088)
3. [How to Install DWM on Arch Linux - Minimal with No Bloat! Dynamic Window Manager](https://youtu.be/jD8BtmMK0do)

### Written Guides

1. [Installation Guide](https://wiki.archlinux.org/title/installation_guide)
2. [Arch Setup Guide](https://asus-linux.org/wiki/arch-guide/)

### Discord

1. [ROG for Linux](https://discord.com/invite/4ZKGd7Un5t)

## Notes / Troubleshooting

### Add boot parameter before the installation ([ref](https://asus-linux.org/wiki/arch-guide/#installing))

On the menu selecting boot option, press `e` , then append this following parameter at the end, then press `enter` to boot.

```
modprobe.blacklist=nouveau
```

### Be careful when partitioning with `fdisk`

I've mistakenly wiped out the whole disk while partitioning because I thought I need to select the whole disk as the target then create new partitions in the free space by specifying the first and last sections according to the sections gab. This approach is not mentioned in any resource from above still I've done this because I didn't see the "free space" like the 2nd video does with `cfdisk`. So I still don't know what is the correct way to create partitions in the free space without affecting existing partitions. So be careful next time. At least, try to find a way to back up first.

### Remember to set up the system time and hardware clock correctly

Otherwise, there might be trouble browsing websites. https://security.stackexchange.com/questions/24704/system-time-and-certificates. I remembered I had them set up properly during the installation but turned out the system clock is not synchronized at the time. After setting it according to the [guide](https://wiki.archlinux.org/title/installation_guide), everything started working again.

Run `timedatectl status` and should get `System clock synchronized: yes`:

```
                              Local time: Sun 2021-11-21 00:17:34 HKT
                      Universal time: Sat 2021-11-20 16:17:34 UTC
                                RTC time: Sat 2021-11-20 16:17:34
                             Time zone: Hongkong (HKT, +0800)
System clock synchronized: yes
                           NTP service: active
                      RTC in local TZ: no
```

### Touchpad settings ([ref](https://wiki.archlinux.org/title/libinput))

Open `/usr/share/X11/xorg.conf.d/40-libinput.conf`, then modify the following section accordingly:

```
Section "InputClass"
        Identifier "libinput touchpad catchall"
        MatchIsTouchpad "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
        Option "Tapping" "on"
        Option "TappingButtonMap" "lrm"
        Option "NaturalScrolling" "true"
EndSection
```

### No audio

After unmuting everything with `alsamixer`, there was still no audio at the time. Having `pipewire` and `pipewire-alsa` installed fixed that.

### No audio in games that runs with Proton

Having the following packages installed fixed that:

```
local/easyeffects 6.1.4-1
    Audio Effects for Pipewire applications
local/gst-plugin-pipewire 1:0.3.40-1
    Multimedia graph framework - pipewire plugin
local/lib32-pipewire 1:0.3.39-1
    Low-latency audio/video router and processor - 32-bit client library
local/libpipewire02 0.2.7-2
    Low-latency audio/video router and processor - legacy client library
local/pipewire 1:0.3.40-1
    Low-latency audio/video router and processor
local/pipewire-alsa 1:0.3.40-1
    Low-latency audio/video router and processor - ALSA configuration
local/pipewire-docs 1:0.3.40-1
    Low-latency audio/video router and processor - documentation
local/pipewire-jack 1:0.3.40-1
    Low-latency audio/video router and processor - JACK support
local/pipewire-media-session 1:0.4.1-1
    Example session manager for PipeWire
local/pipewire-pulse 1:0.3.40-1
    Low-latency audio/video router and processor - PulseAudio replacement
```

The list of packages is shared from a member of the ROG for Linux discord channel. I installed all of them at once, so I don't know which of them exactly fixed the issue.

### `dwm` media key keybindings

Import [XF86keysym.h](https://cgit.freedesktop.org/xorg/proto/x11proto/tree/XF86keysym.h) into the `config.h`. The volume up/down keys are included in it.

### Start X with fish shell ([ref](https://wiki.archlinux.org/title/Fish#Start_X_at_login))

Add the following to the bottom of the `~/.config/fish/config.fish`.

```
# Start X at login
if status is-login
    if test -z "$DISPLAY" -a "$XDG_VTNR" = 1
        exec startx -- -keeptty
    end
end
```

### Important font packages

```
awesome-terminal-fonts
noto-fonts
noto-fonts-cjk
noto-fonts-emoji
```

### Only need drivers mentioned in the ASUS Linux guide ([ref](https://asus-linux.org/wiki/arch-guide/#custom-kernel-drivers-fixes-hardware-support))

Remember to install `nvidia-dkms` along with the custom kernel and patches, which are all mentioned in the guide.

### Multi-screen ([ref](https://wiki.archlinux.org/title/Multihead))

Create `/etc/X11/xorg.conf.d/10-monitor.conf` and add the following to set the screen position:

```
Section "Monitor"
        Identifier "eDP-1"
        Option "Primary" "true"
EndSection

Section "Monitor"
        Identifier "HDMI-1"
        Option "Above" "eDP-1"
EndSection
```
