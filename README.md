# HP LaserJet P1102 / P1102w — macOS automatic-version patcher

A community-made, double-clickable installer helper for legacy HP LaserJet drivers on macOS. It **downloads the original driver image directly from HP**, updates the version check in the installer package's `drivers/Distribution` file, builds a new `.pkg` locally, and opens the standard macOS Installer.

**This project does not include or redistribute any HP driver binaries.**

## Installation (for end users)

1. Download `Install_HP_LaserJet_AutoVersion.zip` from **[Releases](https://github.com/OrangeBlitz/HewlettPackardPrinterDrivers-goldengate-and-beyond/releases/tag/LaserJetP1102)** on GitHub.
2. Unzip it and double-click `Install_HP_LaserJet_AutoVersion.command`.
3. If macOS blocks the unsigned script, review it before approving it in **System Settings → Privacy & Security**. Only run code that you trust.
4. The script detects your macOS version, downloads the original HP DMG, patches and rebuilds the package, then opens the standard installer.
5. Complete the installation. Open **System Settings → Printers & Scanners** and add your printer.

The generated package remains in `~/Downloads/` with a name such as `HewlettPackardPrinterDrivers-macOS-27-patched.pkg`. The patcher does not request administrator privileges; the system installer may ask for your password. An internet connection and sufficient free disk space are required.

## Automatic macOS version detection

The script reads the installed macOS version using `sw_vers -productVersion`, extracts its **major version**, and sets the installer version limit to the **next major version** (`major + 1`, followed by `.0`).

| Detected macOS | Value written to `drivers/Distribution` |
| --- | --- |
| 26.x | `27.0` |
| 27.0 / 27.x | `28.0` |
| 28.0 / 28.x | `29.0` |
| 29.x | `30.0` |

Inside the expanded package, the script updates **exactly two** occurrences in `InstallationCheck()`, using `28.0` below only as an example for macOS 27:

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

This preserves the original `> 0` condition. The value is an **installation-version gate**, not a guarantee that the printer works under that macOS version. The script deliberately stops if the original package's `Distribution` file does not contain exactly one match for each of the two expected fields.

## Known working configuration and limitations

- **HP LaserJet P1102/P1102w, macOS 27 Golden Gate:** the project maintainer reports successful printing **over USB** with the adapted driver. This is user-reported testing, not independent certification.
- Printing over Wi-Fi and other connection types is **not verified** by this project.
- Future macOS releases may require changes beyond an installer-version check, even if the script successfully generates a `.pkg`.
- If HP removes or restructures the original installer, the script may stop. It does not use third-party mirrors.
- The locally rebuilt `.pkg` is **not signed or notarized by HP or Apple**. Depending on macOS settings, installation may require additional approval.
- This script does not disable Gatekeeper, change system security settings, or modify printer-driver binaries.
- The script expects macOS 11 or newer and does not claim support for every included HP printer model.

## Credits and acknowledgements

- **[Kartones](https://blog.kartones.net/post/macos-sequoia-hp-laserjet-p1102-drivers/)** — for documenting the original HP LaserJet compatibility workaround and the approach to downloading, expanding and patching HP printer packages.
- **[jackcheng](https://github.com/jackcheng/hp-p1102w-tahoe)** — for publishing and documenting the HP LaserJet P1102w community installer for macOS Tahoe, which inspired the Golden Gate adaptation.
- **HP Inc.** — author and owner of the original printer driver software, which users download separately from HP's server.

## Disclaimer

**This is an unofficial community modification, not developed, endorsed, approved, or supported by HP Inc. or Apple Inc.** It is provided **“as is”**, without warranties of functionality, security or compatibility. Use it at your own risk. This project does not distribute HP printer-driver binaries; use of the original HP software remains subject to the applicable HP licence. HP and LaserJet are trademarks of their respective owners.
