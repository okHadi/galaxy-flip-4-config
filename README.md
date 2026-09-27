# Galaxy Z Flip 4 setup notes

The top half of this Galaxy Z Flip 4's inner screen was unusable. Android still put content there. This setup shrinks the UI and moves it into the working bottom half so the phone can still be used.

The phone runs Android 16. No root, bootloader unlock, or flashing was used.

## How it works

1. Connect the phone to a laptop by USB. ADB, an Android command-line tool, lets the laptop send commands to the phone after the connection is approved on the phone.
2. Use ADB to change the UI size and position. Keep the full width and fit the content into the bottom half.
3. Put the display commands in a script that Termux runs on the phone. Termux uses its own ADB connection to the same phone to run those commands.
4. Add a **1 Fix Display** home-screen icon with Termux:Widget. It runs the script without typing commands each time.

The laptop connection was used to set up and check the fix. The shortcut runs on the phone, but needs its on-phone ADB connection to be available.

## The setup

- Screen size: `1080x1240`, with density `280`.
- A display offset of `(0,700)` moved the UI into the bottom half.
- Termux could run the display script through an on-phone ADB connection.
- A **1 Fix Display** icon was added to the home screen and confirmed visible.

## Banking apps and debug settings

Some banking apps block access when debug settings are on. We tested settings that let one app reach its login screen while keeping USB ADB available for future laptop use.

That test worked with `adb_enabled=2`, developer options at `0`, and wireless debugging at `0`. Adding the same settings to the shortcut failed because the direct settings commands did not work inside Termux. This error is separate from the display fix. See the setup notes for the tests.

## After a reboot

The screen moves back toward the middle. The size and density stay the same. Tap **1 Fix Display** to run the display script again and move the UI back to the bottom.

Reboot also changed some debug settings. The bank-setting tests and the separate ADB command error are covered in the notes below.

## More details

- [Setup and checks](docs/setup.md)
- [What did not work and why](docs/failed-attempts.md)

These notes describe this phone's setup. Results may differ on another phone or Android version.
