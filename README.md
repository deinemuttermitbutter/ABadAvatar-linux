# BadBuilder (Linux port) - by Claude

A Python port of [Pdawg's BadBuilder](https://github.com/Pdawg-bytes/BadBuilder),
which downloads, extracts, and lays out an Xbox 360 BadUpdate exploit USB
drive. This port targets Linux (built/tested for MX Linux / Debian-based
distros) and drops the Windows-only disk formatting step.

## What's different from the original

- **No disk formatting.** The original used raw Win32 disk APIs to lay down
  a custom FAT32 filesystem (needed for >32GB drives). That code can't run
  on Linux. This port assumes **you've already formatted your USB drive as
  FAT32** and just asks for its mount path (e.g. `/media/you/USBDRIVE`).
  If your drive is over 32GB, format it FAT32 manually first - on Linux you
  can use `gparted`, or `mkfs.fat -F 32 /dev/sdX1` from a terminal.
- **XEX patching uses Wine.** The homebrew/NAND-flasher patching step shells
  out to `XexTool.exe`, which is a Windows-only binary with no Linux build.
  This port runs it through Wine. Install Wine first:
  ```
  sudo apt install wine
  ```
  If Wine isn't installed, the build still completes - that one patch step
  is just skipped with a warning.
- **Menus are numbered**, not arrow-key selection, so it behaves
  predictably in any terminal.

Everything else - downloading the latest release assets from GitHub,
extracting archives, copying files into the right folders on the USB drive,
and adding homebrew apps - works the same as the original.

## Setup

```bash
cd badbuilder-linux
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

## Usage

```bash
python3 main.py
```


## Project layout

```
main.py                    entry point
badbuilder/
  builder.py                main flow (menus, download/extract/copy orchestration)
  console.py                rich-based prompts/menus (replaces Spectre.Console)
  constants.py               working dirs, download URLs/repos
  github.py                  GitHub release lookup + file downloading
  archive.py                 .7z / .zip / .tar extraction
  filesystem.py               directory mirroring, file copy
  patch.py                    XEX patching via Wine
  homebrew.py                  homebrew app add/remove/list menu
```

## Credits

Same as upstream - all the actual exploit tooling is built by:
- **Grimdoomer:** [BadUpdate](https://github.com/grimdoomer/Xbox360BadUpdate)
- **InvoxiPlayGames:** [FreeMyXe](https://github.com/FreeMyXe/FreeMyXe)
- **Byrom90:** [XeUnshackle](https://github.com/Byrom90/XeUnshackle)
- **Swizzy:** [Simple 360 NAND Flasher](https://github.com/Swizzy/XDK_Projects)
- **Team XeDEV:** XeXMenu
- **Pdawg:** original BadBuilder (Windows)
