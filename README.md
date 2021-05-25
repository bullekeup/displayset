# displayset - A simple display auto-setup script tool using xrandr

This script loads configurations for displays, from \*.cfg files located in /etc/displayset/ directory (or the one passed as --cfg-path or -p).

Runs xrandr with pre-defined args along with custom commands (setting wallpaper or anything else) if matching display EDID on given output is connected.

Comes with an optional udev rule to be support monitor hotplug (rule triggered at each drm event, needs displayset to be installed as /usr/bin/displayset). I don't use it anymore (got displayset as a keybind so trigger it manually connecting / disconnecting a monitor), but it should still work.

I don't use it anymore (now called in my bspwmrc), but to run it at startup with LightDM, add to /etc/lightdm.conf :
display-setup-script=<path/to/displayset>.

You can also add it to your .xinitrc or any other X startup script.

Usefull with tiling / minimalist window managers (bspwm, i3, awesome, dwm, XMonad, Openbox, etc...)

Config files are loaded in alphanum order, and all that match a given OUTPUT / EDID pair triggers a xrandr command (along with custom commands). They are loaded sequentially : if two config files match same OUTPUT / EDID pair, only the first one found triggers commands. If no display matches or a disconnection occurs on a configured OUTPUT, displayset disables it (using xrandr --off arg).

Displayset uses /tmp/displayset-configured-outputs as a tmp file to remember which monitors it has already configured in previous calls to prevent re-running them. When a screen is disconnected, the corresponding entry is removed from the file.

You can force-run a new setup for all screens with -f or --force.

## Configuration format

Configuration is easy : each of the following keys accept a value on the following line(s).
A blank line is the end of the current value and start for searching a new one.

See the example config in sample folder.

OUTPUT: Following line is the output to configure (xrandr output name).
Must match connected display output.

EDID: Following lines is the display EDID (as given by xrandr --props).
Must match connected display EDID (allow different configs for different displays on same output).

ARGS: Arguments to setup the display for xrandr (without the --output arg, auto-generated by displayset).

CUSTOM_CMDS_PLUGGED: Following lines are custom commands to run after xrandr ('nitrogen --restore' to set the wallpaper for example). There are directly evaluated as shell commands.

CUSTOM_CMDS_UNPLUGGED: Same but when screen is disconnected.

### Udev rules not triggered with NVIDIA card and proprietary driver

*!! If you have an NVIDIA card and use proprietary drivers, you need to activate KMS as follows, or the udev rule won't work (driver will not trigger events)*

Add the following line to your nvidia modprobe config file (typically /etc/modprobe.d/nvidia.conf)

``` bash
options nvidia_drm modeset=1
```
The driver name *nvidia_drm* name may change between distros. *nvidia_drm* is the driver name in Arch.

### AUR package

displayset is available on the AUR, thought may not be up-to-date. Search on https://aur.archlinux.org/packages/ or install through an AUR helper (ex: yay -S displayset displayset-udev-rules displayset-doc)



Feel free to submit PRs for modifications and improvements

