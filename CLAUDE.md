# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a GitHub Pages repository for the DoorMan project by NVIDIA GEAR Team, featuring an interactive scroll-driven demo website showcasing humanoid door manipulation simulations. The site demonstrates "Infinite Visual Randomizations" powered by IsaacLab and "Robust Policy Transfer" for RGB Sim-to-Real applications.

## Repository Structure

- `index.html` - Main interactive demo page with scroll-driven video frame animation
- `README.md` - Basic project description
- `media/` - Video demonstration files and extracted frames:
  - `Pull Solid Door Sim Render.mp4` - Original pull door simulation video
  - `push_solid_door.mp4` - Original push door simulation video
  - `push_solid_door_no_texture.mp4` - Push door without textures
  - `frames_pull_door/` - 293 extracted JPG frames from pull door sequence
  - `frames_push_door/` - 283 extracted JPG frames from push door sequence
- `font/` - Custom NVIDIA Sans font file (NVIDIASansVF_NALA_W_Wght.woff2)

## Technical Architecture

The website uses a sophisticated scroll-driven animation system:
- **GSAP ScrollTrigger** for scroll-based animations
- **HTML5 Canvas** for rendering video frames at 4K resolution (3840x2160)
- **Frame scrubbing** technique for smooth, scroll-controlled video playback
- **Two sequential animations** with crossfade transitions between door manipulation scenarios
- **Responsive design** with mobile-optimized layouts

The JavaScript dynamically loads and renders individual frames based on scroll position, creating a cinematic presentation controlled by user scrolling.

## Development Context

This is a static website project with no build process or package dependencies. All JavaScript libraries (GSAP) are loaded via CDN. The site is designed to showcase robotics/simulation research results in an engaging, interactive format.

## Common Tasks

- **Content updates**: Edit `index.html` for text, animations, or layout changes
- **Media updates**: Replace video files or frame sequences in `media/` directory
- **Styling changes**: Modify the inline CSS in `index.html` (lines 7-161)
- **Animation tuning**: Adjust GSAP timeline and scroll trigger settings in JavaScript (lines 196-435)
- No build, test, or lint commands are required
- Changes are deployed automatically via GitHub Pages when pushed to the main branch