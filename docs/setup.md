# Setup and checks

## 1. The problem and the plan

The top half of the inner screen was not usable, but Android still used the full screen. Buttons and other content could end up in the bad area. Making everything smaller alone would not solve this: the UI also had to move down.

The plan was to keep the full width, shorten the UI, and move it into the working bottom half. A laptop was used for setup. A home-screen shortcut was added so the display commands could be run again from the phone.

## 2. Connect the laptop to the phone

ADB is part of Google's [Android SDK Platform Tools](https://developer.android.com/tools/releases/platform-tools). It lets a computer send commands to an Android phone.

For an initial USB connection:

1. Install Platform Tools on the laptop and make sure the `adb` command is available.
2. On the phone, open **Settings → About phone → Software information**. Tap **Build number** seven times to show Developer options.
3. Open **Developer options** and turn on **USB debugging**.
4. Connect the phone with a USB cable that supports data.
5. Unlock the phone and accept its **Allow USB debugging** prompt for the laptop.
6. Run this on the laptop:

```sh
adb devices -l
```

A row ending in or containing `device` as its connection state means ADB can reach the phone. `unauthorized` means the phone has not approved that computer. An empty list means ADB has not found a device; it does not prove the cable is unplugged.

These are initial connection steps. Later bank tests changed the debug settings, so they do not describe every setting left on the phone.

### Where to run the commands

Run this on the laptop to open a shell on the phone:

```sh
adb shell
```

The `wm`, `cmd`, and `settings` commands below run inside that shell. For a single command, you can also run `adb shell wm size` directly from the laptop. Type `exit` to leave the phone shell.

### The phone has a second ADB connection

The USB connection lets the laptop control the phone. The shortcut uses a different connection: Termux's ADB client connects back to Android on the same phone.

The successful script check used `127.0.0.1:5555`. Here, `127.0.0.1` means the phone itself. A listening ADB service and an approved Termux ADB key are needed; installing Termux alone does not create that connection.

ADB supports enabling port 5555 with `adb tcpip 5555` from an existing laptop connection. This is separate from Android's **Wireless debugging** pairing flow, which uses other ports. The chat confirmed that port 5555 worked during the script check, but did not establish how it would be made available after every reboot.

## 3. Use the bottom half of the screen

| Setting | Value |
| --- | --- |
| Physical screen | `1080x2640` |
| Override size | `1080x1240` |
| Override density | `280` |
| Display offset | `(0,700)` |

The size and density can be set from an ADB shell:

```sh
wm size 1080x1240
wm density 280
```

Size alone did not set the needed position. The display script used these shell commands:

```sh
cmd window folded-area 0,1400,1080,2640 &&
cmd device_state state 1 &&
sleep 2 &&
cmd window folded-area reset &&
cmd device_state state 3 &&
sleep 2 &&
cmd device_state state reset
```

These state numbers were used on this phone. Do not assume they mean the same thing on another model.

The checked result was an offset of `(0,700)`. The UI occupied physical rows 1400–2640. The offset is not the same as the physical starting row.

Checks used:

```sh
wm size
wm density
dumpsys display | grep mDisplayOffset
```

The keyboard was kept as it was. A reboot kept size and density, but changed the offset to `(0,0)`, moving the UI back toward the middle. The home-screen shortcut runs the display script again to move it back to the bottom.

## 4. Run the fix from Termux

Termux, Termux:Widget, and Termux:Boot were installed. They were exempt from battery saving. Termux:Boot was allowed to receive the boot event.

- **Termux** runs the shell script and the phone's ADB client.
- **Termux:Widget** provides the home-screen shortcut.
- **Termux:Boot** starts scripts after boot. The manual shortcut is the way to run the display fix again when the UI moves back.

Scripts were first placed in `/sdcard/Download/flipsetup/`. An installer copied them into Termux's private home folder.

The laptop can place files in shared storage with `adb push`. It cannot normally write straight into Termux's private folder. That is why the files were staged in shared storage, then copied by an installer running inside Termux. These notes describe the files used on the phone; this repo does not include those scripts.

| Termux file | Purpose |
| --- | --- |
| `~/.termux/flip-core.sh` | Shared display script |
| `~/.termux/flip.conf` | Saved density values |
| `~/.termux/boot/10-flip.sh` | Run the script at boot |
| `~/.shortcuts/1 Fix Display` | Run from a home-screen icon |
| `~/.shortcuts/tasks/` | Background fix and zoom entries |

The shared script has fix, boot, shortcut, zoom, density, and status modes. Saved values were `DENSITY=280`, `ZOOM_IN=420`, and `ZOOM_OUT=280`. The active density stayed at `280`. The zoom entries were installed but not checked end to end.

The script used Termux's ADB client to connect to `127.0.0.1:5555`. It also had a fallback that scanned ports 30000–50000 with `nmap`. That fallback was not confirmed working.

The laptop could not write directly into Termux's private folder. The working route was to open Termux and send keyboard input with `adb shell input`. This ran the installer inside Termux. No manual typing on the phone was needed.

After a wrong interpreter path was fixed, the display check passed. Its log showed the local ADB connection, size `1080x1240`, density `280`, offset `(0,700)`, and exit code `0`. It found the display already in the right position, so this check alone did not prove repair from a bad position.

The new log was `/sdcard/Download/flipsetup/flip.log`. Older runs used `boot.log` in the same folder.

## 5. Add the home-screen icon

The script still existed when the icon was missing. The working launcher flow was:

1. Hold an empty area on the home screen.
2. Open **Widgets** and search for **Termux**.
3. Choose **Termux shortcut**, the `1x1` option.
4. Tap **Add**, then choose **1 Fix Display**.
5. Confirm **Add to Home screen**.

The launcher showed the icon and confirmed it was added. The final icon was not tapped as part of that check.

## 6. Keep ADB while opening a banking app

Some banks block their apps when debug settings are enabled. One tested app showed a message asking for developer options and USB debugging to be turned off.

These combinations were tried:

| `adb_enabled` | `development_settings_enabled` | Result |
| --- | --- | --- |
| `1` | `1` | Blocked |
| `2` | `1` | Blocked |
| `2` | `2` | Blocked |
| `2` | `0` | Login screen appeared with wireless debugging at `0` |

The working test used these commands from an ADB shell:

```sh
settings put global development_settings_enabled 0
settings put global adb_wifi_enabled 0
settings put global adb_enabled 2
```

USB ADB remained available in that test. The login fields were confirmed twice. No sign-in or payment was tested. The app's exact check was not found; this does not prove that all banks treat `2` the same way.

After reboot, the values were `adb_enabled=1`, developer options `0`, and wireless debugging `1`. Setting ADB back to `2` worked from the laptop. The bank was not confirmed working again with wireless debugging still at `1`.

The shortcut and boot wrapper were later given direct `/system/bin/settings` commands. Those commands failed inside Termux. This was a separate bank-setting step; the display script passed its check. Running the settings commands through ADB was suggested, but was not tested from the shortcut.
