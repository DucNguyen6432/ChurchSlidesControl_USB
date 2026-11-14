# BinSlide

A lightweight, portable presentation app for Windows. Copy one `BinSlide.exe` to a USB stick, plug it into any PC, pick a `.pptx` file, and present. Nothing to install, no PowerPoint needed.

BinSlide only presents. It has no slide editing, no clock and no auto-update, so that startup and slide changes stay fast.

## Features

- **Single portable exe.** Python, Qt and every dependency are bundled. The host PC needs nothing installed.
- **Opens any `.pptx`** from Desktop, Downloads or anywhere else on the machine. The file is read fully into memory and closed straight away.
- **Automatic two-screen setup.** With a projector or second display connected:
  - The **projector** shows the full slide, borderless, full screen, with no toolbars and no mouse cursor.
  - The **laptop** shows the **Presenter View**: Previous, Current and Next slides side by side, a slide counter, large navigation buttons and a Quick Jump readout.
- **Clicker and Bluetooth remote support.** Works with anything that sends keyboard keys (Page Up/Down, arrows, Space). Slides are pre-rendered in the background, so a button press only swaps an image on both screens.
- **Quick Jump.** Type a slide number, then Enter, to jump straight there. Esc cancels.
- **Grid view (slide sorter).** Press `G` to see every slide as a thumbnail and click one to jump to it.
- **Smooth transitions.** A short cross-fade on the projector, cancelled instantly by the next key press.
- **Black and white screen** for pausing the show.
- **Screen hot-plug.** Connecting or disconnecting a display re-arranges the windows automatically.

## Using BinSlide

1. Plug in the USB stick and run `BinSlide.exe`. The first start takes a second or two while it unpacks.
2. Choose your `.pptx` in the file dialog. You can also drag a `.pptx` file onto `BinSlide.exe`.
3. If BinSlide finds content it cannot draw, or fonts missing on this PC, it tells you before the show starts. Choose **Present anyway** or **Cancel**.
4. Present. Close the app and everything is gone from memory.

## Keyboard and clicker controls

All keys work no matter which window has focus.

| Action | Keys |
|---|---|
| Next slide | Right, Down, Page Down, Space, Enter, N |
| Previous slide | Left, Up, Page Up, Backspace, P |
| First / last slide | Home / End |
| **Quick Jump** | Type digits (up to 4), then **Enter**. **Esc** cancels, **Backspace** deletes a digit |
| **Grid view** | `G` (press again or Esc to close) |
| Black screen | B or `.` (period) |
| White screen | W or `,` (comma) |
| Exit | Esc twice (once to arm, once to confirm), or the **End show** button |
| One monitor only: switch between slide and presenter view | Tab |
| Swap which screen is the projector | S |

Notes:

- Quick Jump numbers above the slide count go to the last slide.
- Navigating while the screen is black or white brings the slide back and moves on.
- Esc needs two presses because many clickers send Esc from their "stop" button, and an accidental exit mid-talk is the worse mistake.
- In Grid view, the arrow keys move the selection, and Enter, Space or a click jumps to the slide. Quick Jump still works there.
- Mouse: on the projector, left click goes forward and right click goes back. The presenter view also has Prev, Next, Grid, Black and End show buttons.

## Multi-monitor behaviour

- **Two or more screens:** the first non-primary screen is the projector, and the primary screen shows the Presenter View. Press `S` to swap them.
- **One screen:** the slide shows full screen. Press `Tab` for the presenter view, and `Tab` again to go back.
- In Windows "Duplicate" display mode there is only one screen, so BinSlide behaves as single-monitor. Use "Extend" for the two-screen layout.

## Building the exe

You need a Windows PC with Python 3.10 or newer (3.12 or 3.13 recommended) and an internet connection.

1. Unzip the project. If Windows blocks files that came from the internet, run this in PowerShell first:
   ```
   Get-ChildItem -Recurse "path\to\BinSlide" | Unblock-File
   ```
2. In the `BinSlide` folder, run `build.bat`. It creates a virtual environment, installs the requirements and runs PyInstaller. This takes a few minutes.
3. Copy `dist\BinSlide.exe` to your USB stick.

If `build.bat` is blocked, run these by hand:

```
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
pyinstaller --clean --noconfirm BinSlide.spec
```

To run from source without building: `python run_binslide.py`.

## Fonts

Slides look right only if the fonts they use are available. BinSlide checks this before the show.

- A missing font is replaced by a similar installed one, falling back to Arial, so text may wrap differently.
- To bring your own fonts, either:
  - put `.ttf`, `.otf` or `.ttc` files in a folder named **`fonts`** next to `BinSlide.exe`, or
  - put them in a **`bundled_fonts`** folder inside the project *before* building, which embeds them in the exe.
- Fonts are loaded for that run only. They are never installed into Windows.

## What it can and cannot draw

BinSlide has its own renderer, so it does not reproduce PowerPoint exactly.

**Drawn:** text (bullets, numbering, alignment, spacing, shrink-to-fit, hyperlink colour), pictures (with cropping, transparency and shaped crops), backgrounds (solid, gradient, picture, tiled), shapes and lines (common presets and freeform shapes), groups, rotation and flipping, and tables.

**Not drawn:**

- Charts, SmartArt and embedded objects (shown as a labelled box, with a warning before the show)
- Shadows, glows and other effects
- Animations, and slide transitions stored in the file (BinSlide uses its own fade)
- Video and audio
- Speaker notes
- Old `.ppt` files and `.ppsx` slide shows (open or save them as `.pptx` first)

If something fails to draw, the presenter bar shows a message like "Slide 5: 1 item(s) couldn't be drawn (...)". Please include it in any bug report.

## What "no footprint" means

BinSlide never writes slides, settings or logs, and never touches the USB stick. Its file dialog is Qt's own (the Windows one keeps a recent-files list in the registry), and Qt settings are redirected to a temporary folder.

It is best effort, not absolute:

- The single-file exe unpacks itself to `%TEMP%\_MEIxxxxxx` and deletes that folder on a normal exit. If you pull the USB stick or kill the process, the folder is left behind. The next BinSlide launch on that PC cleans up such leftovers.
- Pulling the stick does not stop a running presentation, because the program already runs from the unpacked copy.
- Windows itself still records that the exe was launched (for example Prefetch and USB history). No application can avoid that.

## Troubleshooting

| Problem | What to do |
|---|---|
| "Smart App Control blocked a file" when running `build.bat` | Unblock the files (see Building). |
| Smart App Control or SmartScreen blocks `BinSlide.exe` | The exe is unsigned. Choose "Run anyway" if offered. If Smart App Control is enforcing, you need a code-signing certificate or a PC where it is off. Test on the real PC beforehand. |
| Build fails with an odd package error | Install Python 3.12 or 3.13, delete the `.venv` folder and run `build.bat` again. |
| Projector shows the same thing as the laptop | Windows is in Duplicate mode. Switch to Extend (`Win + P`). |
| Projector and laptop are the wrong way round | Press `S`. |
| Text wraps differently than in PowerPoint | A font is missing. See Fonts. |
| A chart or diagram is a grey box | Not supported. Export it as a picture in PowerPoint and insert that instead. |

## Project layout

```
run_binslide.py        entry point
BinSlide.spec          PyInstaller recipe (one-file exe)
build.bat              one-click build on Windows
requirements.txt       PySide6, python-pptx, PyInstaller
binslide.marker        lets a later run recognise orphaned temp folders
binslide/
  app.py               file picking, screen layout, keyboard handling, Quick Jump, grid
  views.py             slide canvas, projector window, presenter window, slide grid
  renderer.py          .pptx to image renderer, render cache and background prefetch
  ooxml.py             theme colours and text-style inheritance (no Qt, unit-testable)
  footprint.py         zero-footprint helpers and private font loading
```

## Status

The theme, colour and text-style logic has been tested against a generated deck. The full GUI and renderer have been written but not yet run on a real Windows machine by the author, so expect some fixes after the first real-world run. Bug reports with a screenshot of the slide next to how it looks in PowerPoint are the most useful.
