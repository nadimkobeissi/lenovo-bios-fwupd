# lenovo-bios-fwupd

_Install BIOS/UEFI firmware updates for modern Lenovo Legion laptops on Linux, without needing Windows!_

lenovo-bios-fwupd converts Lenovo Windows BIOS update `.exe` files (Insyde H2OFFT based) into fwupd-compatible `.cab` files that can be installed from Linux.

Many Lenovo laptops don't receive BIOS updates through [LVFS](https://fwupd.org/), so the only official update path is a Windows `.exe`. This script extracts the firmware from that `.exe`, reads your system's EFI System Resource Table (ESRT) to get the correct GUID, and packages everything into a `.cab` that `fwupdmgr` can install natively.

## Requirements

- `7z` (p7zip)
- `gcab`
- `fwupdmgr` (fwupd)

## Downloading the BIOS update

1. Go to [Lenovo PC Support](https://pcsupport.lenovo.com/).
2. Find your laptop model (e.g. search for "Legion Pro 7 16IAX10H").
3. Navigate to **Drivers & Software** and filter by the **BIOS/UEFI** component. For example, for the Legion Pro 7 16IAX10H, [this](https://pcsupport.lenovo.com/fr/fr/products/laptops-and-netbooks/legion-series/legion-pro-7-16iax10h/downloads/driver-list/component?name=BIOS%2FUEFI&id=5AC6A815-321D-440E-8833-B07A93E0428C) is the direct link.
4. Download the latest BIOS update `.exe` file.

## Usage

```bash
./lenovo-bios-fwupd.sh <bios_update.exe>
```

The script will:

1. Extract the `.exe` archive with `7z`.
2. Locate the `.fd` firmware file inside.
3. Parse the BIOS version string from the filename.
4. Read your system's firmware GUID and current version from the ESRT.
5. Generate fwupd-compatible metainfo XML.
6. Package everything into a `.cab` file.

Then install the resulting `.cab`:

```bash
sudo fwupdmgr install <version>.cab --allow-reinstall --no-reboot-check
```

**You will need to add `OnlyTrusted=false` to `/etc/fwupd/fwupd.conf` since the resulting .cab file will not be signed.**

Reboot to apply the update. The UEFI firmware will apply the capsule during boot.

**Important:** Ensure AC power is connected and battery is above 30% before installing. Do **not** interrupt the reboot after installation.

## Tested laptops

| Model | Status |
|---|---|
| Legion Pro 7 16IAX10H | Tested, working |

This script should work on other Lenovo laptops that use Insyde H2OFFT-based BIOS updates with a `.fd` firmware file inside the `.exe`. If you test it on another model, please open an issue or PR to update this table.

## License

GPLv2. See [LICENSE](LICENSE).
