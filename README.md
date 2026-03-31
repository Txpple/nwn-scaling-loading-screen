# NWN:EE Scaling Loading Screen

A custom loading screen system for **Neverwinter Nights: Enhanced Edition** that properly scales loading screen images to any resolution and aspect ratio. Instead of stretching or cropping, it displays the image at the correct proportions with soft-edged black bars (letterboxing or pillarboxing) where needed.

## Features

- Maintains 16:9 aspect ratio at any screen resolution
- Adds letterboxing (top/bottom bars) on tall screens and pillarboxing (side bars) on wide screens
- Smooth fade at bar/image boundaries instead of hard edges
- Compensates for NWN:EE UI scaling so the image isn't zoomed in
- Supports 16:9 loading screen images of any resolution

## Installation

1. Copy the following files into your module's override directory or a hak pak:
   - `loadscreen.mtr`
   - `loadscreen_vs.shd`
   - `loadscreen_fs.shd`
   - `pnl_loadscreen.mdl`
   - `loadscreens.2da`
   - Your loading screen textures as TGA or DDS (e.g. `LS_TTR_01.tga`)

2. Add the NWScript snippet from `loadscreen_nss.txt` to your module's **OnEnter** or area **OnExit** script. This passes the player's UI scale setting to the shader so it can compensate correctly. NWN's stock shaders do not expose a UI scale uniform, so this scripting step is required.

## Custom Loading Screens

This mod supports 16:9 images of any size. To add your own:

1. Create your loading screen image as a TGA or DDS file in 16:9 aspect ratio
2. Add a new row to `loadscreens.2da` with the `BMPResRef` column pointing to your filename (without extension)

## How It Works

The system uses a custom GLSL shader pipeline:

- **Vertex shader** scales the loading screen quad to fill the viewport, reads the player's UI scale via a scriptable uniform, and adjusts UV coordinates beyond the 0–1 range to mark where black bars should appear.
- **Fragment shader** renders black for any UV outside 0–1, with a smoothstep fade zone for soft edges at the boundary.
- **Model** provides a 96×54 unit quad (16:9 ratio) that the shaders scale to fit the screen.

## License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.
