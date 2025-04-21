# obsidian_for_wsl
Want to use Obsidian from WSL?
<br><br>
This script handles the download for you, so you can use Obsidian’s GUI for files inside WSL (Debian / Ubuntu).

## Usage Instructions:
1. Save the code (for example copy and paste the code below into a file named download_obsidian.sh in your WSL environment)
2. Make it executable (```chmod +x download_obsidian.sh```)
3. Run the script (```./download_obsidian.sh```)
4. You can now launch Obsidian (Ubuntu) from the Windows Start Menu (recommended), or by running the command ```obsidian``` in WSL (*D-Bus related warnings can be ignored if the app runs correctly*)
5. Recommended - use the "Open folder as vault" option and go to Home to select the folder you are working with

**Note:** Requires curl and wget. To install:
```sudo apt-get update && sudo apt-get install -y curl wget```

## Script:
```bash

#!/usr/bin/env bash

set -euo pipefail

# install the ALSA sound lib if missing (needed to start Obsidian)
if ! dpkg -s libasound2 >/dev/null 2>&1; then
  echo "libasound2 missing - installing..."
  sudo apt-get update -qq
  sudo apt-get install -y libasound2t64
fi

# remove any existing Obsidian installers to avoid conflicts
find . -maxdepth 1 -type f -name 'obsidian_*_amd64.deb*' -delete

# grab download link for the newest 64‑bit .deb release
deb_link=$(
  curl -fsSL https://obsidian.md/download \
  | grep -oE 'https://github[^"]+/obsidian_[0-9]+\.[0-9]+\.[0-9]+_amd64\.deb' \
  | head -n1
)

# download
echo "downloading $deb_link"
wget -qO obsidian_latest.deb "$deb_link"

# install (or upgrade) Obsidian
sudo apt-get install -y ./obsidian_latest.deb

echo -e "\nscript finished successfully\n"
```
