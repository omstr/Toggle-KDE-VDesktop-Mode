# Toggle-KDE-VDesktop-Mode
Some instructions from what I've done to be able to toggle KDE 6.7's new per-display independent Virtual Desktop switching mode with a shortcut (&amp; also taskbar presence))


# 1 - Toggle Script

`mkdir -p ~/.local/bin`
`nano ~/.local/bin/toggle-vdesktop-mode.sh`

Paste:
```
#!/bin/bash

# read the current status from config key
CURRENT_MODE=$(kreadconfig6 --file kwinrc --group Windows --key PerOutputVirtualDesktops)

# toggle the value and send a desktop notification
if [ "$CURRENT_MODE" == "true" ]; then
    kwriteconfig6 --file kwinrc --group Windows --key PerOutputVirtualDesktops "false"
    notify-send "Virtual Desktops" "Linked Screens (Dependent)" -i plasma
else
    kwriteconfig6 --file kwinrc --group Windows --key PerOutputVirtualDesktops "true"
    notify-send "Virtual Desktops" "Split Screens (Independent)" -i plasma
fi

# force KWin to reload the configuration instantly
qdbus org.kde.KWin /KWin reconfigure
```

Ctrl + O. Enter. Ctrl + X.

### 1.5 - Make script executable
`chmod +x ~/.local/bin/toggle-vdesktop-mode.sh`

# 2 - Add keyboard shortcut
Go to:
- System Settings
  - Shortcuts
    - Custom Shortcuts.

Add a new **_Command_** shortcut (+Add New (top right)). Set the path to your script: ~/.local/bin/toggle-vdesktop-mode.sh. Then search for it in Shortcuts & set key combination. 


