# Calibre Install

Documentation and scripts for setting up Calibre with DeDRM plugins on Arch Linux.

## Contents

- [DeDRM Kindle Guide](docs/dedrm-kindle-guide.md) - Complete guide for removing DRM from Kindle ebooks

## Quick Install

```bash
# Install Calibre
sudo pacman -S calibre

# Install DeDRM plugins
cd /tmp
curl -L -o DeDRM.zip "https://github.com/noDRM/DeDRM_tools/releases/download/v10.0.3/DeDRM_tools_10.0.3.zip"
unzip DeDRM.zip
calibre-customize -a DeDRM_plugin.zip
calibre-customize -a Obok_plugin.zip

# Install KFX Input (for modern Kindle formats)
curl -L -o KFX_Input.zip "https://plugins.calibre-ebook.com/291290.zip"
calibre-customize -a KFX_Input.zip
```

## Installed Plugins

| Plugin | Purpose |
|--------|---------|
| DeDRM | Remove DRM from Kindle, Adobe, B&N, Kobo |
| Obok DeDRM | Extract Kobo books |
| KFX Input | Read Amazon KFX format |
| DeACSM | Download ACSM library ebooks |
| KindleUnpack | Unpack Kindle/MOBI files |

## License

Documentation only - see individual plugin licenses.
