---
author: albert
tags: linux kde-plasma
---
Instructions how to add to KDE Plasma 5 panel a button for toggling of windows tiling.
<!--more-->

## Pre-requirements
1. PC running Kubuntu (or other distro with KDE Plasma 5)
1. KWin extension Kröhnkite: [https://github.com/esjeon/krohnkite](https://github.com/esjeon/krohnkite)
1. Plasma applet Command Output: [https://github.com/Zren/plasma-applet-commandoutput](https://github.com/Zren/plasma-applet-commandoutput)

## Bash scripts

### krohnstatus
Script to check whether Kröhnkite script is enabled or not.

```bash
#!/bin/bash
# ---------------------------------------------------------------------- #
# Script checks and prints Krohnkite KWin script status (enable/disable) #
# ---------------------------------------------------------------------- #

# check if script is active
krohnkite_status=`cat ~/.config/kwinrc | grep krohnkiteEnabled= | rev | cut -d"=" -f1 | rev`

# output status
if $krohnkite_status; then
    echo "|T|"
else
    echo "|F|"
fi
```

### krohntoggler
Script to toggle (activate/deactivate) Kröhnkite script.

```bash
#!/bin/bash
# ------------------------------------------------------------ #
# Script toggles Krohnkite KWin script status (enable/disable) #
# ------------------------------------------------------------ #

# check if script is active
krohnkite_status=`cat ~/.config/kwinrc | grep krohnkiteEnabled= | rev | cut -d"=" -f1 | rev`

# select new status
if $krohnkite_status; then
    new_status=false
else
    new_status=true
fi

# toggle script status
kwriteconfig5 --file kwinrc --group Plugins --key krohnkiteEnabled $new_status
qdbus org.kde.KWin /KWin reconfigure
```

## Config
1. You can download the scripts from my [GitHub](https://github.com/AlbertRtk/my_bash_scripts)
2. Place them in a directory, e.g. `~/bin`. If it's not, add the directory to the path
```bash
export PATH=$PATH:~/bin
```
3. Make sure the files are executable
```bash
chmod +x krohnstatus krohntoggler
```
4. Add Commend Output Applet to your Plasma Desktop/Panel. Right click on the applet and select `Configure Command Output...`
5. In the input field under *Command* type `krohnstatus` and in the input field under *Click* type `krohntoggler`

<img width="400px" src="/assets/images/blog/2021-03-14/krohnkite_config.png" alt="Krohnkite config">  

6. You can play with other options and configure it according to your needs

Done! Now you can toggle tiling manager with one click. Created button will display `|T|` if tiling is active, and `|F|` when the script is disabled and windows are in floating mode.