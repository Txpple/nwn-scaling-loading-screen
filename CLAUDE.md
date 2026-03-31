# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Neverwinter Nights: Enhanced Edition (NWN:EE)** custom loading screen mod that replaces the default loading screen with one that properly scales to any screen resolution and aspect ratio, adding letterboxing/pillarboxing with soft-edged black bars instead of stretching or cropping.

## Architecture

The system has four interconnected parts:

1. **Vertex Shader (`loadscreen_vs.shd`)** — Scales the loading screen quad to fill the viewport. Reads the player's UI scale setting via `scriptableFloat1` (SHADER_UNIFORM_1) to counter-scale so UI scaling doesn't zoom the image. Adjusts UVs beyond 0–1 range to signal where black bars should appear based on screen vs. mesh aspect ratio (16:9 baseline).

2. **Fragment Shader (`loadscreen_fs.shd`)** — Renders black bars for UV coordinates outside 0–1, with a configurable `fadeZone` smoothstep for soft edges at the bar/image boundary.

3. **Material (`loadscreen.mtr`)** — Binds the two shaders together as a custom material applied to the loading screen model.

4. **Model (`pnl_loadscreen.mdl`)** — Aurora MDL model with a 96×54 unit foreground quad (16:9 ratio) referencing the `loadscreen` material, plus a background quad. Exported from Blender via NeverBlender.

### Supporting Files

- **`loadscreen_nss.txt`** — NWScript snippet (for module OnEnter / area OnExit) that reads the player's UI scale via `GetPlayerDeviceProperty` and passes it to the shader as `SHADER_UNIFORM_1`.

## Key Constants

- Mesh dimensions: 96×54 units (16:9), defined in both the MDL and vertex shader
- `basePixelsPerUnit`: 100.0 (mesh-to-pixel conversion)
- UI scale range: clamped 1.0–1.7
- `fadeZone`: 0.01 UV-space (soft edge width in fragment shader)

## Deployment

Files go into a NWN:EE module's override directory or a hak pak. The NWScript snippet must be added to the module's enter script to pass UI scale to the shader uniform — NWN's stock shaders have no uniform for the user's UI scale, so it must be scripted in the module. This mod supports 16:9 images of any size. To use custom loading screen images, update `loadscreens.2da` with new entries referencing your own TGA files.
