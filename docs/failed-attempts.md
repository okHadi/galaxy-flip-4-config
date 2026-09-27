# Failed attempts and open issues

## Display and boot

- **Changing size alone:** Did not restore the needed position. The folded-area and device-state steps were used for that.
- **Relying on reboot to keep the position:** Size and density survived, but the offset reset to `(0,0)`.
- **Assuming the boot hook was missing:** Old logs showed both failed and successful boot or widget runs. The exact reason for the later boot failure was not found.
- **Wrong script interpreter:** Some staged scripts used `/data/data/com.termux/files/usr/sh`, which does not exist. The core script and fix/boot wrappers were changed to `/data/data/com.termux/files/usr/bin/bash`, then installed again. The display check passed after this fix. Other entries were not fully checked.
- **Treating a missing log as proof:** No new log did not prove that private scripts had been deleted. Later checks showed that the shortcut file still existed.

## Getting files into Termux

- **Direct file access from laptop ADB:** Android denied access to Termux's private home folder.
- **Granting shell the `RUN_COMMAND` permission:** The grant command returned without an error, but running the service still failed with a permission error.
- **Sending command extras to TermuxActivity:** Opened Termux but did not run the test script.
- **Termux's file receiver:** Opened a save dialog. Saving and running the file were not completed through this route.
- **Direct access to Termux's document provider:** Denied. It required access through Android's file picker.
- **Termux's other file provider:** Also denied access because shell lacked `RUN_COMMAND` permission.
- **Editing only the shared-storage copy:** Inspected installers copied scripts into Termux. Changing the source file alone was not enough to update that installed copy.
- **Looking for symlinks:** An early search failed because the computer expanded Android paths locally. That result said nothing about files on the phone. The inspected installers used copies; not every installed path was checked.

The simple working route was to type into the open Termux terminal through ADB. A test command ran under Termux's own user. The same route then ran the installer.

## Shortcut creation

Opening the shortcut activity sometimes brought the app's info screen to the front. Early taps did not confirm a pinned icon.

It was wrong to conclude that ADB could not help create the shortcut. Driving the launcher worked. Later, opening the shortcut picker with its clear-top flag also produced the **Add to Home screen** prompt. Confirming **Add** placed the icon.

## Banking settings

- **Setting both debug values to `2`:** The tested app still blocked access. Developer options had to be `0` in the successful test.
- **Running `/system/bin/settings` directly in Termux:** Failed with a Java error, even though Termux had `WRITE_SECURE_SETTINGS`. Both reads and writes failed in this test. That permission alone did not make this command-line route work.
- **Assuming unchanged values meant success:** The laptop still read ADB `2` and developer options `0`, but these values were already set before the shortcut ran. They did not prove that the shortcut wrote them.
- **Blank screenshots or no UI text:** Did not prove the bank check passed. Waiting longer and finding actual login fields gave better evidence.
- **Looking at APK strings:** Did not reveal the app's exact comparison. APKs were inspected, but no banking app was patched.

The next proposed fix was to send the settings commands through Termux's working ADB connection. It was not installed or tested. The home icon currently points to a script whose direct settings step is known to fail.

## ADB connection and reboot

At times, `adb devices` showed no phone even though it was connected. The cause was not established. Claims that the cable, USB mode, or key prompt was definitely responsible were not backed by the checks.

An ADB auto-enable app had boot receivers and a reference to the wireless debugging setting. That made it worth checking, but did not prove it reset USB ADB to `1`.

It was also not proved that every ADB restart needs a new key approval, that ADB access is permanent, or that automatic display repair and the bank settings cannot work together.

## Work that was stopped

- Cover-screen widgets were explored, then dropped. No finished cover-screen setup is claimed here.
- Testing a second banking app was deferred.
- Automatic repair after reboot remains unconfirmed. A boot wrapper was installed, but its settings step has the same failure as the shortcut.

## Next useful check

Fix the shortcut's settings step, then test the actual home icon. Check the new log, screen size, density, offset, debug values, USB connection, and the bank's login screen. Only then test a reboot.
