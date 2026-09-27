# Galaxy Z Flip 4 setup notes

Notes from setting up a Galaxy Z Flip 4 on Android 16. The goal was to use the working bottom half of the inner screen, keep USB ADB access, and open banking apps that check debug settings.

No root, bootloader unlock, or flashing was used.

## What worked

- Screen size: `1080x1240`, with density `280`.
- A display offset of `(0,700)` moved the UI into the bottom half.
- Termux could run the display script through an on-phone ADB connection.
- A **1 Fix Display** icon was added to the home screen and confirmed visible.
- One tested banking app reached its login screen with `adb_enabled=2`, developer options set to `0`, and wireless debugging set to `0`. USB ADB still worked.

## What is not finished

- The home shortcut's ADB-setting step failed. The icon exists, but it is not yet a fully working combined fix.
- A reboot kept the screen size and density, but reset the display offset and some debug settings.
- Automatic repair after reboot is not confirmed.
- Banking login and payments were not tested. Results may differ by app or Android update.

## More details

- [Setup and checks](docs/setup.md)
- [Failed attempts and open issues](docs/failed-attempts.md)

These are notes from this phone, not a ready-to-run installer. They leave out bank names, account details, device IDs, and screenshots.
