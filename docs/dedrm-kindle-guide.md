# Calibre DeDRM Setup Guide for Kindle Books

This guide covers installing Calibre on Arch Linux and configuring plugins to remove DRM from Kindle ebooks.

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

### Method 1: Kindle for PC/Mac (Recommended)

This is the most reliable method for books purchased from Amazon.

#### Step 1: Install Older Kindle for PC

Amazon's newer Kindle apps use hardened DRM. You need version **1.17 or earlier**:

- **Windows**: Kindle for PC 1.17
- **Mac**: Kindle for Mac 1.17

Disable auto-updates after installation.

#### Step 2: Download Books

1. Open Kindle for PC/Mac
2. Sign in with your Amazon account
3. Download the books you want to convert
4. Books are stored in:
   - Windows: `C:\Users\<username>\Documents\My Kindle Content\`
   - Mac: `~/Library/Application Support/Kindle/My Kindle Content/`

#### Step 3: Import into Calibre

1. Open Calibre
2. Click **Add books** or drag `.azw` / `.azw3` files into Calibre
3. DeDRM automatically removes protection during import
4. Convert to your preferred format (EPUB, PDF, etc.)

### Method 2: Hardware Kindle Serial Number

For books downloaded directly to a Kindle e-reader:

#### Step 1: Find Your Kindle Serial Number

On your Kindle device:
- Go to **Settings** > **Device Options** > **Device Info**
- Note the 16-character serial number

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

### Method 3: Using adb for Android Kindle App

For books in the Kindle Android app:

1. Enable USB debugging on Android device
2. Use `adb` to pull books from:
   ```
   /data/data/com.amazon.kindle/files/
   ```
3. Import into Calibre

## Troubleshooting

### "DRM Removal Failed"

- Ensure you're using Kindle for PC/Mac version 1.17 or earlier
- Check that the DeDRM plugin is properly configured
- Try re-downloading the book in Kindle app

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

## File Formats

| Format | Description | DeDRM Support |
|--------|-------------|---------------|
| `.azw` | Older Kindle format (Mobipocket-based) | Yes |
| `.azw3` | Kindle Format 8 (KF8) | Yes |
| `.kfx` | Newest Kindle format | Yes (with KFX Input) |
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

## Legal Notice

DRM removal should only be performed on ebooks you have legally purchased for personal backup purposes. Respect copyright laws in your jurisdiction.

## References

- [DeDRM Tools GitHub](https://github.com/noDRM/DeDRM_tools)
- [Calibre Plugin Index](https://plugins.calibre-ebook.com/)
- [MobileRead Forums - KFX Input](https://www.mobileread.com/forums/showthread.php?t=291290)
- [Calibre Official Site](https://calibre-ebook.com/)
