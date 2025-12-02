# DoorMan - NVIDIA GEAR Lab

Interactive demo website for the DoorMan project showcasing humanoid door manipulation with sophisticated scroll-driven animations.

🌐 **Live Site**: [https://doorman-humanoid.github.io](https://doorman-humanoid.github.io)

## Features

- **Scroll-Driven Frame Animation**: Cinematic background animation controlled by scrolling
- **Interactive D3.js Charts**: Training progress visualization with growing line effects
- **Lazy Loading**: Progressive frame loading for fast initial page load
- **Fully Responsive**: Optimized layouts for desktop and mobile
- **Modern Visual Effects**: Gradient text, loading animations, hover effects

## Built With

- Pure HTML5/CSS3/JavaScript (no build process required)
- [GSAP](https://greensock.com/gsap/) - Animation library
- [D3.js](https://d3js.org/) - Data visualization
- Built entirely with [Claude Code](https://claude.ai/code)

## Using This as a Template

This website can serve as a template for other research project websites. See `CLAUDE.md` for comprehensive documentation on all visual features and how to customize them.

### ⚠️ IMPORTANT Warnings Before Using as Template

#### 1. Google Analytics Tag - MUST REMOVE OR REPLACE

**Lines 4-12 in `index.html` contain Google Analytics tracking for the DoorMan project.**

If you use this template without modification, all your website visitors will be tracked under the DoorMan project's analytics!

**Action Required:**
```html
<!-- DELETE this entire block from index.html lines 4-12 -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-RJSDHZD03T"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-RJSDHZD03T');
</script>
```

Then add your own Google Analytics tracking ID, or omit it entirely.

#### 2. NVIDIA Sans Font - Proprietary License

**The `font/NVIDIASansVF_NALA_W_Wght.woff2` file is NVIDIA's proprietary font.**

If you are **NOT** affiliated with NVIDIA, you **MUST**:
1. Delete the `font/` directory
2. Remove the `@font-face` declaration (lines 18-20 in `index.html`)
3. Change `font-family: 'NVIDIA Sans'` to a different font (line 32 in `index.html`)

**Recommended font alternatives:**
- **Inter** (Google Fonts) - Modern, clean
- **Roboto** (Google Fonts) - Standard sans-serif
- System fonts: `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`

## Quick Start

### Local Development

```bash
# Clone the repository
git clone https://github.com/doorman-humanoid/doorman-humanoid.github.io.git
cd doorman-humanoid.github.io

# Start local server (required for CORS)
python -m http.server 8000

# Open in browser
open http://localhost:8000
```

### Deployment

Push to GitHub and enable GitHub Pages:
1. Settings → Pages
2. Source: Deploy from branch `main` / root folder
3. Site will be live at: `https://your-username.github.io/your-repo/`

No build process required - GitHub Pages serves the static HTML directly.

## Documentation

- **`CLAUDE.md`** - Comprehensive documentation for AI agents and developers wanting to customize this template
- **`index.html`** - Single-file website (~2000 lines)

## Project Structure

```
.
├── index.html              # Main website (single file)
├── CLAUDE.md              # Detailed documentation
├── README.md              # This file
├── font/                  # NVIDIA Sans font (⚠️ proprietary - remove if not NVIDIA)
│   └── NVIDIASansVF_NALA_W_Wght.woff2
└── media/                 # All assets
    ├── frames_pull_door/  # 293 frames for scroll animation
    ├── frames_push_door/  # 283 frames for scroll animation
    ├── data.csv           # Training data for D3.js chart
    ├── logos/             # University/company logos
    ├── demos_loc_1/       # Demo videos
    ├── demos_loc_2/       # Demo videos
    └── sim/               # Simulation videos
```

## Customization Guide

For detailed customization instructions, see `CLAUDE.md` which documents:
- All 9 visual features and how they work
- How to replace frame sequences, videos, and data
- Performance optimization tips
- Troubleshooting common issues
- Step-by-step workflows for using as a template

**Quick customization checklist:**
1. ⚠️ Remove Google Analytics tag (lines 4-12)
2. ⚠️ Replace NVIDIA font or remove it
3. Replace frame sequences in `media/frames_*` folders
4. Update `media/data.csv` with your training data
5. Replace videos in `media/` directories
6. Update text content (search "DoorMan" and replace)
7. Update authors, affiliations, abstract
8. Update logos in `media/logos/`
9. Update BibTeX citation

## Performance

- **Initial load**: ~5 seconds (loads 20 initial frames)
- **Total page size**: ~200MB (all frames + videos)
- **Time to interactive**: ~5 seconds (remaining frames load in background)
- **Mobile optimized**: 4x frame subsampling, reduced resolution

## Credits

- **Built with**: [Claude Code](https://claude.ai/code) by Anthropic
- **Animation**: [GSAP](https://greensock.com/gsap/) by GreenSock
- **Visualization**: [D3.js](https://d3js.org/)
- **Font**: NVIDIA Sans (proprietary - NVIDIA only)

## License

The code structure and visual effects are provided as a template for the research community. However:

- ⚠️ **NVIDIA Sans font**: Proprietary - remove if not NVIDIA-affiliated
- ⚠️ **Google Analytics tag**: DoorMan project tracking - must remove/replace
- GSAP: GreenSock License
- D3.js: BSD License

---

**Questions?** See `CLAUDE.md` for comprehensive documentation, or open an issue.
