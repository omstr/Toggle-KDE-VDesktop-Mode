# Toggle-KDE-VDesktop-Mode
Some instructions from what I've done to be able to toggle KDE 6.7's new per-display independent Virtual Desktop switching mode with a shortcut (&amp; also taskbar presence))


# 1 - Toggle Script

`mkdir -p ~/.local/bin`

`nano ~/.local/bin/toggle-vdesktop-mode.sh`

Paste (Ctrl+Shift+V) into nano:
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

1. Ctrl + O. Enter. (save)
2. Ctrl + X. (exit)

### 1.5 - Make script executable
`chmod +x ~/.local/bin/toggle-vdesktop-mode.sh`


<!-- how add bit of space? -->

#

# 2 - Add keyboard shortcut
Go to:
- System Settings
  - Shortcuts (Input & Output)

Then:

1. Add a new **_Command_** shortcut (+Add New (top right)).
2. Set the path to your script: /home/**<YOUR_USERNAME>**/.local/bin/toggle-vdesktop-mode.sh.
    <!-- does $USER work here? -->
3. Then search for it in Shortcuts & set key combination. (I set it to Shift+F1)

#
# 3 - Taskbar presence (optional visual cue)

Have to download a widget from the plasma widget store called **Command Output**. 
FYI:
- [KDE Store link](https://store.kde.org/p/2136636)
- [GitHub repository link](https://github.com/Zren/plasma-applet-commandoutput)

1. Right click taskbar, click "Add or Manage Widgets"
2. Click "Get New" (Download new plasma widgets)
    a. Search for "Command Output" & download.
3. Go back into "Add or Manage Widgets" mode (Top Left)
4. Click (or drag?) the new **Command Output** widget.
    a. Right click the new widget on the taskbar to configure.
    b. I added THIS as the command to use Emojis to indicate which mode is active:
        - also I set the interval to 2500ms (can also do 1000ms)
    `[[ "$(kreadconfig6 --file kwinrc --group Windows --key PerOutputVirtualDesktops)" == "true" ]] && echo "🟢" || echo "⚪"`



#
# Automated Script

Here's a script that supposedly does Step 1 to 2 (& downloads Step 3 widget) for you automatically (Generated with an LLM & didn't test after I did it) 
- The keybind is `Meta(Win/Super/all-the-other-bs-keywords-used-to-describe-this-key) + X`

```
#!/bin/bash

# Define paths
SCRIPT_DIR="$HOME/.local/bin"
TOGGLE_SCRIPT="$SCRIPT_DIR/toggle-desktops.sh"

echo "⚙️ Creating the toggle script..."
mkdir -p "$SCRIPT_DIR"

cat << 'EOF' > "$TOGGLE_SCRIPT"
#!/bin/bash
CURRENT_MODE=$(kreadconfig6 --file kwinrc --group Windows --key PerOutputVirtualDesktops)

if [ "$CURRENT_MODE" == "true" ]; then
    kwriteconfig6 --file kwinrc --group Windows --key PerOutputVirtualDesktops "false"
    notify-send "Virtual Desktops" "Linked Screens 🔗" -i plasma
else
    kwriteconfig6 --file kwinrc --group Windows --key PerOutputVirtualDesktops "true"
    notify-send "Virtual Desktops" "Split Screens 🟢" -i plasma
fi

qdbus org.kde.KWin /KWin reconfigure
EOF

chmod +x "$TOGGLE_SCRIPT"

echo "⌨️ Automating global hotkey binding (Meta+X)..."
# Register the command globally under KDE's shortcut architecture
kwriteconfig6 --file kglobalshortcutsrc --group "KWidgetsServiceScript" --key "_launch" "$TOGGLE_SCRIPT,none,Toggle Monitor Desktops"
kwriteconfig6 --file kglobalshortcutsrc --group "KWidgetsServiceScript" --key "Toggle Monitor Desktops" "Meta+X,none,Toggle Monitor Desktops"

# Reload global shortcuts daemon to apply
qdbus org.kde.kglobalaccel /kglobalaccel org.kde.kglobalaccel.reconfigure

echo "📥 Downloading and installing 'Command Output' Widget..."
# Installs directly from the KDE store API repository
kpackagetool6 --type Plasma/Applet -i "https://store.kde.org/p/2136636/"

echo "🚀 Setup complete!"
echo "--------------------------------------------------------"
echo "1. Press Meta+X to verify the toggle functionality."
echo "2. Right-click your panel -> Add Widgets -> Add 'Command Output'."
echo "3. Right-click the widget -> Configure."
echo "4. Paste this exact line into the 'Command' field:"
echo "   [[ \"\$(kreadconfig6 --file kwinrc --group Windows --key PerOutputVirtualDesktops)\" == \"true\" ]] && echo \"🟢\" || echo \"⚪\""
echo "--------------------------------------------------------"

```
