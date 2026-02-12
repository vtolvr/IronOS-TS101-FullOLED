# IronOS-TS101-FullOLED

Full 128x32 OLED display support for the Miniware TS101 soldering iron.

## Important Notice

This is an experimental fork specifically for TS101 with full OLED support.

**Tested on:** TS101 hardware only  
**NOT tested on:** TS100, TS80, TS80P, or other Miniware devices  
**Status:** Works great on my TS101. Your mileage may vary.

For stable, official firmware, use the main [IronOS repository](https://github.com/Ralim/IronOS). If this breaks your iron, don't blame me (or Ralim).

## What This Actually Does

The official IronOS firmware for TS101 only uses the upper-left corner of the display. Not because the rest of the screen is broken, but because getting the full 128x32 OLED working required some specific tweaks that weren't implemented yet.

This fork fixes that.

**Before:** Only about 96x16 pixels working (upper-left corner)  
**After:** Full 128x32 pixels across the entire screen

Basically, you paid for a 128x32 display. Now you get to use all of it.

## What Changed

The main fix was changing one clock divider value from 0xD3 to 0xD5. Turns out the TS101's OLED controller is picky about timing, and the wrong divider meant it only refreshed part of the screen reliably.

**Modified files:**

**Core/Drivers/OLED.hpp**
- Changed OLED_DIVIDER from 0xD3 to 0xD5
- Moved some display refresh code around for better organization
- Cleaned up some redundant I2C calls

**Core/Drivers/OLED.cpp**
- Updated refresh logic to handle the full 512-byte framebuffer
- Proper initialization sequence for the SSD1306 controller

**Core/BSP/Miniware/Setup.cpp**
- TS101-specific OLED initialization
- Warning: This might not play nice with other Miniware models, which is why this is a separate repo

The technical explanation: The TS101 uses a 128x32 SSD1306 OLED that needs a 0xD5 clock divider for proper timing. The previous 0xD3 value worked fine for the smaller displays on other irons, but caused the TS101 to only update the top-left quadrant reliably.

## Credits

**showier-drastic** - Did the actual work figuring out the fix. I just compiled it, tweaked the code a bit, and wrote this README.

**Ralim** - Created IronOS in the first place. None of this exists without his work.

**VioletEternity** - Reverse engineered the TS101 hardware to make support possible at all.

This is a fork of [IronOS](https://github.com/Ralim/IronOS) by Ralim. All the cool features are his. The OLED fix is from showier-drastic. I'm just the guy who got it building and working.

## Installation

**What you need:**
- A TS101 soldering iron
- A USB-C cable
- About 2 minutes

**Steps:**

1. Download the firmware from [Releases](../../releases) (grab the TS101_EN.hex)

2. Put your TS101 in DFU mode:
   - Unplug the iron
   - Hold down the front button (the one closer to the tip)
   - While holding it, plug in the USB-C cable
   - The iron should show up as a USB drive

3. Flash it:
   - Copy the .hex file to that USB drive
   - Wait for it to finish copying
   - The file will rename itself to .RDY if it worked, or .ERR if it didn't
   - If you get .ERR, don't panic. Just copy the file again without deleting the .ERR file. Usually works the second time.

4. Unplug, power it up with your normal power supply, and enjoy your newly functional full display.

**Want to go back to official firmware?**

Download from [Ralim/IronOS releases](https://github.com/Ralim/IronOS/releases) and flash it the same way. Your iron won't be mad at you.

## Building from Source

If you want to compile this yourself (maybe you don't trust random hex files on the internet, which is fair):

**Windows (using MSYS2):**
```bash
# Install the things
pacman -S mingw-w64-x86_64-arm-none-eabi-gcc python3 python3-pip make git

# Get the code
git clone --recurse-submodules https://github.com/vtolvr/IronOS-TS101-FullOLED.git
cd IronOS-TS101-FullOLED/source

# Python stuff
python3 -m venv ironos-venv
source ironos-venv/bin/activate
pip install bdflib

# Build it
make -j8 model=TS101 firmware-EN
```

Your firmware ends up in `source/Hexfile/TS101_EN.hex`

**Docker (works everywhere):**
```bash
git clone --recurse-submodules https://github.com/vtolvr/IronOS-TS101-FullOLED.git
cd IronOS-TS101-FullOLED
./scripts/deploy.sh
cd source/
./build.sh -l EN -m TS101
```

## Known Issues

- Haven't tested this on TS100, TS80, or TS80P. It probably won't work on those, and might actively break things. Stick to TS101 only.
- The UI layouts work fine at 128x32, but there's probably room for optimization now that we have more screen real estate.

## Contributing

Found a bug? Got an improvement? Open an issue or PR.

If you're having problems with base IronOS features (not related to the display), report those to the [main IronOS repo](https://github.com/Ralim/IronOS/issues) instead.

## License

Same license as upstream IronOS. Check the LICENSE file for details.

## Disclaimer

This is custom firmware. Flashing it might void your warranty. It works on my TS101, but I can't promise it'll work on yours. Keep a backup of the official firmware just in case.

Don't sue me if your iron catches fire or becomes sentient or something.

## Questions?

Open an [issue](../../issues) and I'll try to help.
