# Calibre DeDRM Setup Guide for Kindle Books

This guide covers installing Calibre on Arch Linux and configuring plugins to remove DRM from Kindle ebooks.

## Important: Kindle Device Compatibility

**Not all Kindle devices support DRM removal.** Amazon introduced hardened DRM in newer devices that no current tool can crack.

### Supported Devices (DRM Removal Works)

| Generation | Examples | Notes |
|------------|----------|-------|
| 10th gen and earlier | Kindle Paperwhite (2018), Kindle Oasis 2/3, basic Kindle (2019) | Serial number method works |
| Kindle for PC 1.17-1.24 | Windows desktop app | Most reliable method |
| Kindle for PC 2.8.0 | Windows (with Epubor Ultimate) | Current working method on Windows |

### Unsupported Devices (Hardened DRM)

| Generation | Examples | Issue |
|------------|----------|-------|
| 11th gen+ (firmware 5.18.5+) | Kindle Paperwhite Signature Edition (2021), Kindle Scribe, Kindle 2022 | Hardened DRM - no current bypass |
| Kindle for PC 2.0+ | Newer Windows versions | KFX format with enhanced DRM |
| Kindle for Android/iOS | Mobile apps | Enhanced DRM, rooting required |

**Bottom line**: If you have an 11th generation or newer Kindle, the serial number method will fail with "Incorrect padding - Wrong key" errors.

### Amazon's February 2025 Changes

On **February 26, 2025**, Amazon removed the "Download & Transfer via USB" option from their website. This means:
- You can no longer download `.azw3` files directly from Amazon's website
- Books must be synced wirelessly to devices or apps
- Kindle Cloud Reader no longer allows downloads

## Installation

### Calibre on Arch Linux

```bash
# Install from official extra repository
sudo pacman -S calibre

# Or using paru/yay for AUR helper users
paru -S calibre
```

Package details: https://archlinux.org/packages/extra/x86_64/calibre/

## Installed Plugins

| Plugin | Version | Purpose |
|--------|---------|---------|
| **DeDRM** | 10.0.3 | Removes DRM from Amazon Kindle, Adobe Adept, Barnes & Noble, Kobo |
| **Obok DeDRM** | 10.0.3 | Extracts Kobo books and Amazon KPF files |
| **KFX Input** | 2.27.1 | Reads Amazon KFX format (required for modern Kindle books) |
| **DeACSM** | 0.0.16 | Downloads books from ACSM license files (library ebooks) |
| **KindleUnpack** | 0.83.8 | Unpacks Kindle/MOBI files into source components |

### Plugin Sources

- **DeDRM & Obok**: https://github.com/noDRM/DeDRM_tools/releases
- **KFX Input**: https://plugins.calibre-ebook.com/291290.zip
- **DeACSM**: https://plugins.calibre-ebook.com/341975.zip
- **KindleUnpack**: https://plugins.calibre-ebook.com/171529.zip

**Note**: DeDRM is NOT available on the official Calibre plugin repository. It must be downloaded from GitHub.

## Installing Plugins

### Via Command Line

```bash
# Download and install DeDRM tools
cd /tmp
curl -L -o DeDRM_tools.zip "https://github.com/noDRM/DeDRM_tools/releases/download/v10.0.3/DeDRM_tools_10.0.3.zip"

# Verify checksum
sha256sum DeDRM_tools.zip
# Expected: 8649e30efb0c26e9cca1131df4c9d02d51eccb5028d396cce857f0fa75a62849

# Extract
unzip DeDRM_tools.zip

# Install plugins
calibre-customize -a DeDRM_plugin.zip
calibre-customize -a Obok_plugin.zip

# Install KFX Input (required for modern Kindle formats)
curl -L -o KFX_Input.zip "https://plugins.calibre-ebook.com/291290.zip"
calibre-customize -a KFX_Input.zip

# Optional: DeACSM for library ebooks
curl -L -o DeACSM.zip "https://plugins.calibre-ebook.com/341975.zip"
calibre-customize -a DeACSM.zip

# Optional: KindleUnpack for inspecting Kindle files
curl -L -o KindleUnpack.zip "https://plugins.calibre-ebook.com/171529.zip"
calibre-customize -a KindleUnpack.zip

# Verify installation
calibre-customize -l | grep -iE "(DeDRM|KFX|Obok)"
```

### Via Calibre GUI

1. Open Calibre
2. Go to **Preferences** > **Plugins** > **Load plugin from file**
3. Select each `.zip` plugin file
4. Restart Calibre

## Removing DRM from Kindle Books

### Method 1: Kindle for PC on Windows (Recommended)

This is the most reliable method for books purchased from Amazon.

#### Step 1: Install Kindle for PC

For **Calibre + DeDRM**:
- Use Kindle for PC version **1.17 to 1.24**
- Download from archive.org: [Kindle for PC 1.17.44183](https://archive.org/details/kindle-for-pc-1-17-44183)

For **Epubor Ultimate**:
- Use Kindle for PC version **2.8.0** (latest version supported)

Disable auto-updates after installation.

#### Step 2: Download Books

1. Open Kindle for PC
2. Sign in with your Amazon account
3. Download the books you want to convert
4. Books are stored in: `C:\Users\<username>\Documents\My Kindle Content\`

#### Step 3: Import into Calibre

1. Open Calibre
2. Click **Add books** or drag `.azw` / `.azw3` files into Calibre
3. DeDRM automatically removes protection during import
4. Convert to your preferred format (EPUB, PDF, etc.)

### Method 2: Hardware Kindle Serial Number (10th Gen and Earlier Only)

**Warning**: This method does NOT work for 11th generation or newer Kindles due to hardened DRM.

For books downloaded directly to a Kindle e-reader (10th gen or earlier):

#### Step 1: Find Your Kindle Serial Number

On your Kindle device:
- Go to **Settings** > **Device Options** > **Device Info**
- Note the 16-character serial number

#### Registered Devices

| Device | Serial Number | DRM Status |
|--------|---------------|------------|
| Kindle Paperwhite Signature Edition (11th gen) | `G001 LG13 3116 00RL` | Hardened DRM (unsupported) |

#### Step 2: Configure DeDRM Plugin

1. In Calibre: **Preferences** > **Plugins**
2. Find **DeDRM** under "File type plugins"
3. Click **Customize plugin**
4. Select **eInk Kindle ebooks**
5. Add your Kindle serial number

#### Step 3: Copy and Import Books

1. Connect Kindle to computer via USB
2. Copy `.azw3` files from Kindle's `documents` folder
3. Import into Calibre

### Method 3: Kindle for PC via Wine on Linux

**Status**: Not recommended - network connectivity issues prevent this from working reliably.

#### Setup (for reference)

```bash
# Install Wine and dependencies
sudo pacman -S wine wine-mono wine-gecko winetricks lib32-gnutls

# Create dedicated Wine prefix
export WINEPREFIX=/home/mlj/.wine-kindle
wineboot --init

# Install Kindle for PC
wine /path/to/KindleForPC-installer-1.17.44183.exe

# Install network components (may help with SSL issues)
winetricks crypt32 winhttp wininet
```

#### Configuration

Configure DeDRM to use the Wine prefix:
1. In Calibre: **Preferences** > **Plugins** > **DeDRM** > **Customize plugin**
2. Select **Kindle for Mac/PC/Wine**
3. Set Wine prefix: `/home/mlj/.wine-kindle`

#### Known Issues

- Amazon's servers reject connections from older TLS/SSL versions
- Kindle for PC 1.17 cannot authenticate with Amazon in Wine
- No reliable workaround exists as of December 2025

### Method 4: Using adb for Android Kindle App

For books in the Kindle Android app:

1. Enable USB debugging on Android device
2. Use `adb` to pull books from:
   ```
   /data/data/com.amazon.kindle/files/
   ```
3. Import into Calibre

**Note**: Modern Android Kindle apps have enhanced DRM that may not be removable.

## Alternative Approaches

If the above methods don't work for your setup:

### Windows Virtual Machine

The most reliable approach for Linux users:
1. Install VirtualBox or QEMU/KVM
2. Install Windows 10/11
3. Install Kindle for PC 2.8.0
4. Use Epubor Ultimate or Calibre + DeDRM

### Acquire an Older Kindle

If you frequently purchase from Amazon:
- 10th generation or earlier Kindles still support DRM removal
- Check serial number prefix: devices starting with `G090` or earlier generally work

### DRM-Free Alternatives

- **Standard Ebooks**: https://standardebooks.org/ (free, high-quality public domain)
- **Project Gutenberg**: https://gutenberg.org/ (free public domain)
- **Kobo with Obok**: Kobo ebooks can be extracted with the Obok plugin
- **Library ebooks via DeACSM**: Many libraries offer ACSM files

## Troubleshooting

### "Incorrect padding - Wrong key" Error

This error indicates **hardened DRM** that cannot be removed:

- **11th gen+ Kindles**: These devices use DRM that no current tool can bypass
- **Newer KFX files**: Some KFX files from recent downloads have enhanced protection

**Solutions**:
- Use Kindle for PC on Windows instead
- Use an older Kindle device (10th gen or earlier)
- Wait for potential DeDRM updates (uncertain timeline)

### "DRM Removal Failed"

- Ensure you're using Kindle for PC version 1.17-1.24 (on Windows)
- Check that the DeDRM plugin is properly configured
- Try re-downloading the book in Kindle app
- Verify your Kindle is 10th generation or earlier

### "KFX Format Not Recognized"

- Ensure KFX Input plugin is installed
- Update to latest KFX Input version
- KFX files from newer Kindle versions may have additional protection

### Plugin Not Working After Calibre Update

```bash
# Reinstall plugins after major Calibre updates
calibre-customize -a /path/to/DeDRM_plugin.zip
calibre-customize -a /path/to/KFX_Input.zip
```

### Checking Installed Plugins

```bash
calibre-customize -l | grep -iE "(DeDRM|KFX|Obok|DeACSM|Kindle)"
```

### Wine Network Issues

If Kindle for PC in Wine cannot connect:
- This is a known issue with SSL/TLS compatibility
- Amazon's servers reject older protocol versions
- No reliable fix exists - use Windows VM instead

## File Formats

| Format | Description | DeDRM Support |
|--------|-------------|---------------|
| `.azw` | Older Kindle format (Mobipocket-based) | Yes |
| `.azw3` | Kindle Format 8 (KF8) | Yes (older devices) |
| `.kfx` | Newest Kindle format | Yes (with KFX Input, older devices) |
| `.mobi` | Mobipocket format | Yes |
| `.prc` | Palm Resource format | Yes |

## Converting After DRM Removal

Once DRM is removed, convert to your preferred format:

```bash
# Command line conversion
ebook-convert "book.azw3" "book.epub"
ebook-convert "book.azw3" "book.pdf"
```

Or use Calibre GUI: Right-click book > **Convert books** > Select output format

## Configuration Files

### DeDRM Plugin Config

Location: `~/.config/calibre/plugins/dedrm.json`

```json
{
  "serials": ["G001LG13311600RL"],
  "configured": true
}
```

## Legal Notice

DRM removal should only be performed on ebooks you have legally purchased for personal backup purposes. Respect copyright laws in your jurisdiction.

## References

- [DeDRM Tools GitHub](https://github.com/noDRM/DeDRM_tools)
- [Calibre Plugin Index](https://plugins.calibre-ebook.com/)
- [MobileRead Forums - KFX Input](https://www.mobileread.com/forums/showthread.php?t=291290)
- [Calibre Official Site](https://calibre-ebook.com/)
- [Kindle for PC 1.17 Archive](https://archive.org/details/kindle-for-pc-1-17-44183)

## Changelog

- **December 2025**: Added documentation on 11th gen Kindle limitations, Wine setup issues, Amazon's February 2025 changes, and alternative approaches
