# HewlettPackardPrinterDrivers-goldengate
Repackage of HewlettPackardPrinterDrivers to support MacOS Golden Gate. 
# HP LaserJet Professional P1102w Driver for macOS 27 Golden Gate

An unofficial, community-modified HP printer driver package adapted for **macOS 27 Golden Gate** and successfully tested.

This project builds on the work of [jackcheng](https://github.com/jackcheng/hp-p1102w-tahoe) and [Kartones](https://blog.kartones.net/post/macos-sequoia-hp-laserjet-p1102-drivers/) to make an older HP driver installer usable on newer macOS versions. Its primary target is the **HP LaserJet Professional P1102w**. The original HP package may contain drivers for additional models, but they have not been individually verified as part of this project.

## Compatibility

- **Target operating system:** macOS 27 Golden Gate.
- **Primary printer:** HP LaserJet Professional P1102w.
- **Installer version limit:** `28.0` (see below). This is a *maximum permitted OS version in the installer check*, **not** a claim of tested macOS 28 support.

> **Important:** Adjusting an installer version check enables installation; it does not by itself guarantee that the printer driver or every connection method works. Printing functionality should be tested on the target Mac.

## Installation (using the prepared package)

1. Download the modified `.pkg` file from this project's release or download page.
2. Double-click the `.pkg` file and follow the on-screen instructions. You may need an administrator password.
3. Connect the printer via USB or ensure that it is reachable over the network.
4. Open **System Settings → Printers & Scanners**, click **Add Printer, Scanner or Fax**, select your printer, and choose the installed HP driver if it is not selected automatically.
5. Print a test page.

If macOS blocks the package, check its origin before approving it through **System Settings → Privacy & Security**. Only install packages from sources you trust.

## What was changed

The `InstallationCheck()` function in the package's `Distribution` file was modified. **Both** occurrences of the macOS version value were set to `28.0`:

```javascript
function InstallationCheck(prefix) {
    if (system.compareVersions(system.version.ProductVersion, '28.0') > 0) {
        my.result.message = system.localizedStringWithFormat('ERROR_25CBFE41C7', '28.0');
        my.result.type = 'Fatal';
        return false;
    }
    return true;
}
```

The first `28.0` sets the installer's version ceiling. The second `28.0` is passed to the localized error message, so the displayed limit matches the actual check.

**How the check behaves:** Because it rejects versions **greater than** `28.0`, macOS 27.x passes this particular check; `28.0` also passes, while versions above `28.0` are rejected. Passing this check is not equivalent to confirmed driver compatibility.

## Rebuilding the package yourself (optional)

If you have the original `HewlettPackardPrinterDrivers.pkg` and want to reproduce the change:

1. Open **Terminal** in the directory containing the `.pkg` file.
2. Expand the installer:

   ```bash
   pkgutil --expand HewlettPackardPrinterDrivers.pkg drivers
   ```

3. Open `drivers/Distribution` in a text editor and update **both** version strings in `InstallationCheck()` as shown above. Save the file.
4. Rebuild the installer:

   ```bash
   pkgutil --flatten drivers HewlettPackardPrinterDrivers-GoldenGate.pkg
   ```

5. Install the newly created `HewlettPackardPrinterDrivers-GoldenGate.pkg` using the instructions above.

These commands assume the source package is named `HewlettPackardPrinterDrivers.pkg`. If you are starting from a differently named package, adjust the filename in the commands. Repacking a package can invalidate its original digital signature; verify the source before installing the result.

## Credits and acknowledgements

This project is based on prior community work:

- **[Kartones](https://blog.kartones.net/post/macos-sequoia-hp-laserjet-p1102-drivers/)** — Documented how to unpack, modify, and rebuild the HP installer for macOS Sequoia and Tahoe, and shared an automation script. These instructions provided the technical basis for the version-check adjustment.
- **[jackcheng](https://github.com/jackcheng/hp-p1102w-tahoe)** — Published a repackaged HP LaserJet P1102w driver for **macOS 26 Tahoe**. This package was the direct starting point for the Golden Gate adaptation.
- **HP Inc.** — Developed the original printer drivers and installer.

**macOS Golden Gate adaptation:** The version references in `InstallationCheck()` were updated to `28.0`, including both `system.compareVersions(...)` and `system.localizedStringWithFormat(...)`.

Thanks to Kartones and jackcheng for helping users extend the useful life of older printers.

## Disclaimer

**This driver package is an unofficial community modification and is not developed, endorsed, or officially supported by HP Inc. or Apple Inc.** It is based on HP's original printer driver software, with modifications made by independent community contributors.

**Use it at your own risk.** The package is provided **“as is”**, without warranties or guarantees of successful installation, printing functionality, compatibility, or security. To the extent permitted by applicable law, the maintainer and contributors accept no liability for any problems, damage, or data loss resulting from its installation or use.

All trademarks belong to their respective owners. The original HP driver files remain subject to their applicable licence terms; this project does not claim ownership of them or grant any additional redistribution rights.

## References

- [Kartones — macOS Sequoia and Tahoe HP LaserJet P1102 drivers](https://blog.kartones.net/post/macos-sequoia-hp-laserjet-p1102-drivers/)
- [jackcheng — hp-p1102w-tahoe](https://github.com/jackcheng/hp-p1102w-tahoe)
