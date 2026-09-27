# Galaxy Z Flip 4 setup notes

Notes from setting up a Galaxy Z Flip 4 on Android 16. The goal was to use the working bottom half of the inner screen, keep USB ADB access, and open banking apps that check debug settings.

No root, bootloader unlock, or flashing was used.

## The setup

- Screen size: `1080x1240`, with density `280`.
- A display offset of `(0,700)` moved the UI into the bottom half.
- Termux could run the display script through an on-phone ADB connection.
- A **1 Fix Display** icon was added to the home screen and confirmed visible.
- One tested banking app reached its login screen with `adb_enabled=2`, developer options set to `0`, and wireless debugging set to `0`. USB ADB still worked.

## After a reboot

The screen moves back toward the middle. The size and density stay the same. Tap **1 Fix Display** to run the display script again and move the UI back to the bottom.

Reboot also changed some debug settings. The bank-setting tests and the separate ADB command error are covered in the notes below.

## More details

- [Setup and checks](docs/setup.md)
- [What did not work and why](docs/failed-attempts.md)

These notes describe this phone's setup. Results may differ on another phone or Android version.
