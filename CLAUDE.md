# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a GitHub Pages repository for the DoorMan project by NVIDIA GEAR Team, featuring an interactive scroll-driven demo website showcasing humanoid door manipulation simulations. The site demonstrates "Infinite Visual Randomizations" powered by IsaacLab and "Robust Policy Transfer" for RGB Sim-to-Real applications.

**IMPORTANT FOR TEMPLATE USERS**: This website was built entirely with Claude Code and features sophisticated visual effects that are well-documented below. Other researchers can use this as a template by providing this repository to Claude Code, which can guide them through customizing content while preserving the visual effects.

## Repository Structure

- `index.html` - Single-file website with embedded CSS and JavaScript (~2000 lines)
- `CLAUDE.md` - This file - comprehensive documentation for AI agents
- `README.md` - Basic project description
- `media/` - All video and image assets:
  - `frames_pull_door/` - 293 JPG frames (frame_0004.jpg to frame_0291.jpg) for first scroll animation
  - `frames_push_door/` - 283 JPG frames (frame_0004.jpg to frame_0287.jpg) for second scroll animation
  - `data.csv` - CSV data for D3.js chart (columns: Step, push, pull, pushbar)
  - `logos/` - University/company logos (PNG format)
  - `demos_loc_1/`, `demos_loc_2/` - Demonstration videos (MP4 format)
  - `sim/` - Simulation videos
- `font/` - Custom NVIDIA Sans font file (NVIDIASansVF_NALA_W_Wght.woff2)

## Technical Architecture

This is a **single-page static website** with no build process or package dependencies. All libraries are loaded via CDN:
- **GSAP 3.12.2** (ScrollTrigger plugin) - Scroll-based animations
- **D3.js v7** - Data visualization

### Key Technologies
- Pure HTML5/CSS3/JavaScript (ES6+)
- HTML5 Canvas API for frame rendering
- CSS Grid and Flexbox for layouts
- CSS custom properties for theming
- Progressive lazy loading for performance

---

## Visual Features Documentation

This section documents all custom visual effects and how to customize them. Each feature includes:
- **Purpose**: What the effect does and where it appears
- **Technical Implementation**: How it works
- **Required Assets**: What files/data are needed
- **Customization Guide**: How to replace with your own content
- **Code Location**: Where to find it in index.html

---

### 1. Scroll-Driven Frame Animation (Main Background)

**Purpose**:
The website's signature feature - a cinematic scroll-controlled animation using hundreds of image frames that play as the user scrolls. Appears as the full-screen background during the first ~1000vh of scrolling.

**Technical Implementation**:
- Uses HTML5 Canvas to render high-resolution frames (3840x2160 on desktop, 1920x1080 on mobile)
- GSAP ScrollTrigger synchronizes scroll position to frame index
- Progressive lazy loading: loads 20 initial frames (10 on mobile), then loads remaining frames in background batches
- Two sequential video sequences with crossfade transition at 50% scroll progress
- Device pixel ratio handling for sharp rendering on retina displays

**Required Assets**:
```
media/frames_pull_door/frame_0004.jpg through frame_0291.jpg (288 frames total)
media/frames_push_door/frame_0004.jpg through frame_0287.jpg (283 frames total)
```
- Frames must be numbered sequentially with 4-digit padding (e.g., frame_0004.jpg)
- Recommended resolution: 3840x2160 (4K) for best quality
- Format: JPG (for smaller file size)

**Customization Guide**:

To replace with your own animation:

1. **Extract frames from your video(s)**:
   ```bash
   ffmpeg -i your_video.mp4 -vf "fps=30" media/frames_sequence1/frame_%04d.jpg
   ```

2. **Update JavaScript configuration** (around line 1228-1241):
   ```javascript
   // First sequence
   const subsampleFactor = isMobile ? 4 : 2; // Adjust frame sampling
   const frameCount1 = Math.floor(288 / subsampleFactor); // Your frame count
   const frameOffset1 = 4; // Starting frame number
   const getFrame1 = index => {
       const frameNum = (index * subsampleFactor) + frameOffset1;
       return `media/your_folder/frame_${frameNum.toString().padStart(4, '0')}.jpg`;
   };

   // Second sequence (optional - can have just one)
   const frameCount2 = Math.floor(278 / subsampleFactor);
   const frameOffset2 = 4;
   const getFrame2 = index => {
       const frameNum = (index * subsampleFactor) + frameOffset2;
       return `media/your_folder2/frame_${frameNum.toString().padStart(4, '0')}.jpg`;
   };
   ```

3. **Adjust scroll height** (line 50):
   ```css
   .content {
       height: 1000vh; /* Adjust for desired scroll duration */
   }
   ```

4. **Modify crossfade timing** (line 1347-1360):
   ```javascript
   // Crossfade happens at 0.5 progress (50% of scroll)
   // Adjust these values to change transition point
   ```

**Performance Notes**:
- Mobile uses 4x frame subsampling (every 4th frame) to reduce memory
- Desktop uses 2x subsampling
- Initial load: Only 20 frames (10 on mobile)
- Background loading: 5 frames per batch using requestIdleCallback
- Total load time: ~5 seconds for all frames on typical connection

**Code Location**:
- HTML Canvas element: line 844
- JavaScript: lines 1160-1365 (canvas setup, frame loading, rendering)
- CSS: lines 31-47 (.background-canvas styling)

---

### 2. Animated D3.js Line Chart with Growing Effect

**Purpose**:
Interactive line chart showing training progress with animated line-drawing effect. Located in the "Teacher-Student-Bootstrap" section (Phase 3). Lines glow on hover with a laser-like effect during growth.

**Technical Implementation**:
- D3.js v7 for data visualization and SVG rendering
- CSV data loaded asynchronously
- 3-point moving average smoothing applied to raw data
- Lines drawn in reverse order (3-2-1) to control z-index
- Custom gradient effects using SVG linearGradient
- GSAP for smooth hover animations

**Required Assets**:
```
media/data.csv
```
CSV format:
```
Step,push,pull,pushbar
0,0.45,0.42,0.48
1000,0.52,0.49,0.55
...
```
- **Step**: X-axis values (iteration numbers)
- **push, pull, pushbar**: Y-axis values as decimals (0.0 to 1.0, will be converted to percentages)

**Customization Guide**:

1. **Replace CSV data**:
   - Maintain same column structure
   - Values should be in range 0.0-1.0 (they're multiplied by 100 for display)
   - Any number of rows is fine

2. **Change line colors** (line 1579-1581):
   ```javascript
   const colors = {
       push: '#bff230',    // Yellow-green
       pull: '#76b900',    // NVIDIA green
       pushbar: '#3f8500'  // Dark green
   };
   ```

3. **Update legend labels** (line 1726-1734):
   ```javascript
   const legendData = [
       { key: 'push', label: 'Your Label 1' },
       { key: 'pull', label: 'Your Label 2' },
       { key: 'pushbar', label: 'Your Label 3' }
   ];
   ```

4. **Adjust Y-axis range** (line 1591-1596):
   ```javascript
   const yScale = d3.scaleLinear()
       .domain([50, 95])  // Min: 50%, Max: 95%
       .range([height, 0]);
   ```

5. **Modify growing animation duration** (line 1693-1695):
   ```javascript
   .duration(2000)  // 2 seconds total draw time
   ```

**Visual Effects**:
- **Growing line animation**: Lines draw from left to right on page load
- **Gradient during growth**: White "laser point" at drawing head, fades to line color
- **Hover glow**: White glow effect on line and legend when hovered
- **Hit area expansion**: Invisible 20px-wide overlay for easier hovering

**Code Location**:
- HTML container: line 895 (`<div id="bootstrap-chart">`)
- JavaScript: lines 1570-1788 (chart creation, data loading, animations)
- CSS: lines 656-663 (#bootstrap-chart styling)

---

### 3. Gradient Text Effects

**Purpose**:
Animated gradient text for highlighting key metrics and terms. Two variants used:
- **Bootstrap text**: Fades in with slide animation (used in section titles)
- **Highlight text**: Always visible, continuously animating (used for "31.7%")

**Technical Implementation**:
- CSS gradient with animated background-position
- `-webkit-background-clip: text` for gradient text effect
- CSS keyframe animations for color shifting
- GSAP for fade-in/slide-in on scroll

**Gradient Colors**:
```css
linear-gradient(120deg, #76b900, #a8e063, #56ab2f, #76b900)
/* NVIDIA green → Light green → Medium green → NVIDIA green */
```

**Customization Guide**:

1. **Add gradient to any text**:
   ```html
   <!-- Auto-visible gradient -->
   <span class="highlight-text">Your Text</span>

   <!-- Animated fade-in gradient (requires GSAP setup) -->
   <span class="bootstrap-text">Your Text</span>
   ```

2. **Change gradient colors** (line 394-396 for bootstrap-text, line 407-409 for highlight-text):
   ```css
   background: linear-gradient(120deg, #color1, #color2, #color3, #color1);
   ```

3. **Adjust animation speed** (line 398):
   ```css
   animation: gradientShift 4s ease infinite; /* 4 seconds per cycle */
   ```

**Code Location**:
- Bootstrap text CSS: lines 389-402
- Highlight text CSS: lines 404-416
- Bootstrap text GSAP animation: lines 1599-1615
- Gradient keyframes: lines 418-425

---

### 4. Loading Screen with Spinner

**Purpose**:
Displays while initial frames load (~5 seconds). Features "Loading Experience..." text with triple-ring spinner in NVIDIA green colors.

**Technical Implementation**:
- Full-screen fixed overlay (z-index: 9999)
- Three concentric spinning rings with different rotation speeds
- CSS animations with cubic-bezier easing
- Automatically hidden via JavaScript when initial frames load
- Smooth 0.8s fade-out transition

**Customization Guide**:

1. **Change loading text** (line 838):
   ```html
   <div class="loading-text">Your Loading Message...</div>
   ```

2. **Adjust spinner colors** (lines 198, 204, 210):
   ```css
   .spinner-ring {
       border-top-color: #76b900; /* Ring 1 */
   }
   .spinner-ring:nth-child(2) {
       border-top-color: #a8e063; /* Ring 2 */
   }
   .spinner-ring:nth-child(3) {
       border-top-color: #56ab2f; /* Ring 3 */
   }
   ```

3. **Modify spinner size** (lines 187-190):
   ```css
   .loading-spinner {
       width: 60px;  /* Desktop */
       height: 60px;
   }
   ```

4. **Change animation speeds** (lines 200, 205, 211):
   ```css
   animation: spin 1.5s cubic-bezier(...) infinite; /* Ring 1 */
   animation-duration: 2s;   /* Ring 2 */
   animation-duration: 2.5s; /* Ring 3 */
   ```

**Auto-Hide Behavior**:
Loading screen automatically hides when:
- Initial frames are loaded (Promise.all resolves)
- Small 300ms delay ensures first frame renders
- Then 800ms fade-out transition

**Code Location**:
- HTML: lines 837-842
- CSS: lines 158-260
- JavaScript (hide logic): lines 1424-1432
- Mobile responsive: lines 825-832

---

### 5. Navigation Bar with Underline Animation

**Purpose**:
Sticky navigation at top with three buttons (Home, Paper Details, arXiv). Green underline bar animates in from center on hover.

**Technical Implementation**:
- Fixed positioning with high z-index (10)
- CSS ::after pseudo-element for underline bar
- CSS transitions for smooth width expansion
- JavaScript smooth scroll for navigation
- Dark gradient overlay behind nav for contrast

**Customization Guide**:

1. **Add/remove navigation buttons** (lines 853-857):
   ```html
   <div class="nav-buttons">
       <a href="#" class="nav-btn" id="nav-home">Home</a>
       <a href="#" class="nav-btn" id="nav-paper">Paper Details</a>
       <a href="#" class="nav-btn" id="nav-arxiv">arXiv</a>
       <!-- Add more buttons here -->
   </div>
   ```

2. **Update button actions** (lines 2062-2078):
   ```javascript
   document.getElementById('nav-your-id').addEventListener('click', function(e) {
       e.preventDefault();
       // Smooth scroll to element
       document.querySelector('.your-section').scrollIntoView({
           behavior: 'smooth',
           block: 'start'
       });
   });
   ```

3. **Change underline color** (line 288):
   ```css
   .nav-btn::after {
       background: #76b900; /* Green underline */
   }
   ```

4. **Adjust underline thickness** (line 287):
   ```css
   height: 3px; /* Underline bar height */
   ```

5. **Modify animation speed** (line 289):
   ```css
   transition: width 0.3s ease; /* Expansion duration */
   ```

**Mobile Behavior**:
- Buttons move below header titles (top: 4.5rem)
- Spans full width with smaller font size
- Reduced gap between buttons

**Code Location**:
- HTML: lines 853-857
- CSS: lines 262-298 (desktop), 805-817 (mobile)
- JavaScript: lines 2062-2078
- Top gradient overlay: lines 147-156

---

### 6. Scroll Indicator with Bounce Animation

**Purpose**:
"Scroll to explore" text at bottom of screen with gentle up-down bounce animation. Fades out when user scrolls past 95%.

**Technical Implementation**:
- Fixed positioning at bottom center
- GSAP fade-in animation (1.5s delay after page load)
- Continuous bounce animation using GSAP repeat: -1, yoyo: true
- ScrollTrigger hides indicator at 95% scroll progress

**Customization Guide**:

1. **Change indicator text** (line 866):
   ```html
   <div class="scroll-indicator" id="scroll-indicator">
       Your Custom Message
   </div>
   ```

2. **Adjust bounce distance** (line 1578):
   ```javascript
   gsap.to(scrollIndicator, {
       y: -10,  // Bounce up 10 pixels
       // ...
   });
   ```

3. **Modify bounce speed** (line 1579):
   ```javascript
   duration: 1.2,  // Seconds per bounce cycle
   ```

4. **Change fade-in timing** (lines 1570-1573):
   ```javascript
   gsap.to(scrollIndicator, {
       opacity: 1,
       duration: 1.5,  // Fade duration
       delay: 1,       // Delay before fade starts
       // ...
   });
   ```

5. **Adjust hide trigger point** (line 1583):
   ```javascript
   scrollTrigger: {
       trigger: ".content",
       start: "95% bottom",  // Hide at 95% scroll
       end: "97% bottom",
       // ...
   }
   ```

**Code Location**:
- HTML: lines 865-867
- CSS: lines 116-127
- JavaScript: lines 1568-1595

---

### 7. Lazy Loading System

**Purpose**:
Optimizes initial page load by deferring non-critical assets. Reduces initial load from ~500MB to ~50-100MB.

**Technical Implementation**:
Two-part strategy:
1. **Progressive frame loading**: Loads 20 initial frames, then batches of 5 in background
2. **Video lazy loading**: Uses `preload="metadata"` on all video elements

**Frame Loading Algorithm**:
```javascript
// Phase 1: Load initial frames (instant)
Load first 20 frames → Enable scrolling

// Phase 2: Background loading (uses requestIdleCallback)
while (frames remaining) {
    Load 5 frames
    Wait for browser idle time
}
```

**Customization Guide**:

1. **Adjust initial frame count** (line 1242):
   ```javascript
   const initialFramesToLoad = isMobile ? 10 : 20;
   // Mobile: 10 frames, Desktop: 20 frames
   ```

2. **Change background batch size** (line 1309):
   ```javascript
   const batchSize = 5; // Frames loaded per batch
   ```

3. **Modify video preload behavior**:
   ```html
   <!-- Current: Load metadata only -->
   <video preload="metadata">

   <!-- Alternative: No preload -->
   <video preload="none">

   <!-- Alternative: Preload entire video -->
   <video preload="auto">
   ```

**Performance Impact**:
- **Before**: 576 frames + 22 videos = ~500MB initial load
- **After**: 40 frames + video metadata = ~50-100MB initial load
- **Time to Interactive**: Reduced from ~30 seconds to ~5 seconds

**Code Location**:
- Frame loading: lines 1240-1329
- Video preload attributes: All `<video>` tags throughout HTML

---

### 8. Mobile Viewport Handling

**Purpose**:
Prevents black bars and layout jumps when mobile browser chrome (address bar, bottom nav) appears/disappears during scrolling.

**Technical Implementation**:
- Uses CSS `100dvh` (dynamic viewport height) instead of `100vh`
- Automatically expands/contracts with browser chrome
- JavaScript avoids overriding CSS height on mobile
- Fallback to `-webkit-fill-available` for older iOS

**Customization Guide**:

Generally works automatically, but if adding new full-screen elements:

```css
.your-fullscreen-element {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    width: 100vw;
    height: 100vh;
    height: 100dvh; /* Dynamic viewport - adapts to browser chrome */
}

@supports (-webkit-touch-callout: none) and (not (height: 100dvh)) {
    /* Fallback for older iOS */
    .your-fullscreen-element {
        height: -webkit-fill-available;
    }
}
```

**Code Location**:
- CSS: lines 31-47 (.background-canvas)
- CSS: lines 55-68 (.dark-overlay)
- CSS: lines 21-29 (body)
- JavaScript: lines 1186-1190, 1218-1222

---

### 9. Video Section Layouts

**Purpose**:
Various video presentation layouts used throughout the site. Three main patterns:

#### Pattern 1: Two-Column Grid (Desktop) / Single Column (Mobile)
Used in: Method section, Real-World Generalization

```html
<div class="video-grid">
    <video autoplay muted loop playsinline preload="metadata">
        <source src="media/your-video1.mp4" type="video/mp4">
    </video>
    <video autoplay muted loop playsinline preload="metadata">
        <source src="media/your-video2.mp4" type="video/mp4">
    </video>
</div>
```

**CSS**: Lines 599-607 (`.video-grid`)

#### Pattern 2: Three-Column Grid with Captions
Used in: Failure Cases, Teleop Comparison

```html
<div class="failure-grid">
    <div class="failure-item">
        <video autoplay muted loop playsinline controls preload="metadata">
            <source src="media/your-video.mp4" type="video/mp4">
        </video>
        <div class="failure-caption">Your caption text</div>
    </div>
    <!-- Repeat for more videos -->
</div>
```

**CSS**: Lines 618-632 (`.failure-grid`, `.failure-item`, `.failure-caption`)

#### Pattern 3: Horizontal Flow with Icons (Teacher-Student-Bootstrap)
Used in: TSB methodology section

```html
<div class="tsb-flow">
    <div class="tsb-item">
        <div class="tsb-content">
            <video class="tsb-video" autoplay muted loop playsinline preload="metadata">
                <source src="media/your-video.mp4" type="video/mp4">
            </video>
            <div class="tsb-label">Phase description</div>
        </div>
    </div>
    <!-- Icons and more phases -->
</div>
```

**CSS**: Lines 569-592 (`.tsb-flow`, `.tsb-item`, `.tsb-content`, `.tsb-video`)

**Video Best Practices**:
1. Always include `preload="metadata"` for lazy loading
2. Use `autoplay muted loop playsinline` for auto-playing videos
3. Add `controls` only when user interaction is needed
4. MP4 format with H.264 codec for best compatibility
5. Remove audio tracks if not needed: `ffmpeg -i input.mp4 -c:v copy -an output.mp4`

---

## Color Scheme

The website uses NVIDIA's green color palette:

```css
/* Primary Colors */
--nvidia-green: #76b900;      /* Main brand color */
--light-green: #a8e063;       /* Lighter variant */
--medium-green: #56ab2f;      /* Medium variant */
--dark-green: #3f8500;        /* Darker variant */
--yellow-green: #bff230;      /* Bright accent */

/* Backgrounds */
--dark-bg: #000;              /* Black background */
--code-bg: #2a2a2a;           /* Code block background */
--overlay-bg: rgba(0,0,0,0.3);/* Dark overlay */

/* Text */
--text-white: #fff;           /* Primary text */
--text-gray: rgba(255,255,255,0.75); /* Secondary text */
```

**Changing the Color Scheme**:
Search and replace hex values in index.html. Main occurrences:
- Lines 92, 288, 409: Navigation and highlights (#76b900)
- Lines 180, 198, 204, 210: Loading spinner
- Lines 1579-1581: D3.js chart lines
- Lines 394-396, 407-409: Gradient text effects

---

## Responsive Design

The website adapts to different screen sizes:

**Breakpoint**: 768px (mobile vs desktop)

**Mobile Optimizations**:
- Canvas uses 1x DPR instead of 2x (memory optimization)
- Frame subsampling: Every 4th frame instead of every 2nd
- Initial frame load: 10 instead of 20
- Font sizes reduced: 2.5rem → 1.8rem for titles
- Padding reduced: 50px → 40px on sections
- Grids collapse to single column
- Navigation buttons span full width below header
- Logos stack vertically

**Desktop Features**:
- Higher resolution canvas rendering
- More frames loaded (smoother animation)
- Multi-column layouts for videos and content
- Larger typography and spacing
- Horizontal logo layout with separators

**Code Location**: Lines 699-833 (`@media (max-width: 768px)`)

---

## Common Customization Workflows

### Workflow 1: Using This as a Template for Your Research

**Goal**: Replace DoorMan content with your own while keeping visual effects.

**Steps**:

1. **Prepare your assets**:
   ```bash
   # Extract frames from your demo video
   ffmpeg -i your_demo.mp4 -vf "fps=30" media/frames_sequence1/frame_%04d.jpg

   # Remove audio from videos (smaller file size)
   ffmpeg -i your_video.mp4 -c:v copy -an output.mp4
   ```

2. **Update text content**:
   - Search for "DoorMan" → replace with your project name
   - Update paper title (line 869-871)
   - Update authors and affiliations (lines 873-888)
   - Replace abstract (lines 1119-1121)
   - Update section titles and descriptions throughout

3. **Replace frame sequences**:
   - Follow instructions in "Section 1: Scroll-Driven Frame Animation"
   - Update frame paths and counts in JavaScript

4. **Update D3.js chart**:
   - Replace `media/data.csv` with your training data
   - Follow instructions in "Section 2: Animated D3.js Line Chart"

5. **Replace videos**:
   - Place your MP4 files in `media/` folders
   - Update `<source src="...">` paths in HTML
   - Keep `preload="metadata"` for performance

6. **Update logos**:
   - Replace PNG files in `media/logos/`
   - Adjust sizes if needed (lines 376-382)

7. **Customize colors** (optional):
   - Follow "Color Scheme" section above
   - Search/replace hex values

8. **Update metadata**:
   - Title tag (line 6)
   - Header titles (lines 850-851)
   - Citation BibTeX (lines 1140-1145)

### Workflow 2: Adding a New Section

**Example**: Adding a "Limitations" section

```html
<!-- Add after existing sections, before Abstract -->
<div class="section">
    <h2 class="section-title">Limitations</h2>
    <div class="section-content">
        <p>Your limitation discussion here.</p>
    </div>

    <!-- Optional: Add videos using patterns from Section 9 -->
    <div class="failure-grid">
        <div class="failure-item">
            <video autoplay muted loop playsinline preload="metadata">
                <source src="media/limitations/video1.mp4" type="video/mp4">
            </video>
            <div class="failure-caption">Example failure case</div>
        </div>
    </div>
</div>
```

### Workflow 3: Adjusting Scroll Animation Duration

**Problem**: Frame animation feels too fast/slow

**Solution**: Adjust content height
```css
/* Line 52 */
.content {
    height: 1000vh; /* Increase for slower, decrease for faster */
}
```

**Rule of thumb**:
- 1000vh ≈ 10 screen heights of scrolling
- For 300 frames at 30fps = 10 seconds of video
- 1000vh / 10s ≈ 100vh per second of video

---

## Performance Optimization

Current optimizations:
1. **Progressive loading**: Only 20 frames initially
2. **Background loading**: Remaining frames load during idle time
3. **Video lazy loading**: Metadata only until needed
4. **Frame subsampling**: 2x on desktop, 4x on mobile
5. **Resolution capping**: Max 3840x2160 on desktop, 1920x1080 on mobile
6. **DPR limiting**: Max 2x on desktop, 1x on mobile
7. **Debounced resize**: 250ms delay on window resize

**If experiencing performance issues**:

1. **Increase subsampling** (line 1230):
   ```javascript
   const subsampleFactor = isMobile ? 6 : 3; // More aggressive
   ```

2. **Reduce initial frames** (line 1242):
   ```javascript
   const initialFramesToLoad = isMobile ? 5 : 10;
   ```

3. **Decrease canvas resolution** (line 1179-1180):
   ```javascript
   const maxDimension = isMobile ? 1280 : 1920; // Lower max
   ```

4. **Compress JPG frames more**:
   ```bash
   ffmpeg -i input.mp4 -vf "fps=30" -q:v 5 frame_%04d.jpg
   # -q:v 5 = more compression (2 = best quality, 31 = worst)
   ```

---

## Deployment

**GitHub Pages Setup**:
1. Push code to `main` branch
2. Go to Settings → Pages
3. Source: Deploy from branch `main` / root folder
4. Site will be live at: `https://your-username.github.io/your-repo/`

**No build required** - GitHub Pages serves static HTML directly.

**Testing locally**:
```bash
# Start local server (needed for CORS on local files)
python -m http.server 8000

# Open browser
open http://localhost:8000
```

---

## Troubleshooting

### Issue: Loading screen never disappears
**Cause**: Frames not loading
**Check**:
- Browser console for errors
- Frame paths are correct (case-sensitive)
- Frames exist in directories

### Issue: Videos not playing on mobile
**Cause**: Missing `playsinline` attribute
**Fix**: Add `playsinline` to all `<video>` tags

### Issue: Black bars on mobile when scrolling
**Cause**: Viewport height not updating
**Check**:
- `100dvh` is being used (lines 38, 63)
- No JavaScript errors preventing render

### Issue: D3.js chart not appearing
**Cause**: CSV not loaded or malformed
**Check**:
- `media/data.csv` exists
- CSV has correct columns: Step, push, pull, pushbar
- Browser console for D3 errors
- Chart container `#bootstrap-chart` exists (line 895)

### Issue: Scroll animation stuttering
**Cause**: Too many frames loaded, browser memory limit
**Fix**:
- Increase subsampling factor
- Reduce max canvas dimensions
- Compress frames more aggressively

---

## IMPORTANT Warnings for Template Users

### ⚠️ Google Analytics Tag - MUST REMOVE

**Lines 4-12 in index.html contain Google Analytics tracking code for the DoorMan project.**

```html
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-RJSDHZD03T"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-RJSDHZD03T');
</script>
```

**If you are using this as a template:**
1. **DELETE the entire Google tag block (lines 4-12)** - Otherwise your visitors will be tracked under the DoorMan project analytics
2. Add your own Google Analytics tag if desired with your own tracking ID

### ⚠️ NVIDIA Sans Font - Licensed Property

**The NVIDIA Sans font (`font/NVIDIASansVF_NALA_W_Wght.woff2`) is NVIDIA's proprietary font.**

**If you are NOT affiliated with NVIDIA:**
1. **DELETE the `font/` directory**
2. **Remove the `@font-face` declaration** (lines 18-20 in index.html):
   ```css
   @font-face {
       font-family: 'NVIDIA Sans';
       src: url('font/NVIDIASansVF_NALA_W_Wght.woff2') format('woff2');
   }
   ```
3. **Change the font-family** in body CSS (line 32):
   ```css
   body {
       font-family: 'Arial', sans-serif; /* Or your preferred font */
       /* ... */
   }
   ```

**Recommended alternatives:**
- **Inter** - Modern, clean sans-serif (Google Fonts)
- **Roboto** - Google's standard sans-serif
- **System fonts**: `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif`

---

## Files You Should NOT Modify (Unless Replacing for Your Project)

- `font/NVIDIASansVF_NALA_W_Wght.woff2` - NVIDIA proprietary font (replace if not NVIDIA-affiliated)
- CDN links for GSAP and D3.js - Ensure version compatibility
- Google Analytics tag in `<head>` - MUST replace with your own or remove entirely

---

## Credits

Built with:
- [GSAP](https://greensock.com/gsap/) - Animation library (GreenSock License)
- [D3.js](https://d3js.org/) - Data visualization (BSD License)
- NVIDIA Sans - Custom font (NVIDIA property)

This template was created entirely using [Claude Code](https://claude.ai/code) by Anthropic.

---

## Questions for Claude Code Agents

When a user provides this repository asking to customize it:

### FIRST - Issue Critical Warnings

**IMMEDIATELY warn the user about:**

1. **⚠️ Google Analytics Tag (lines 4-12 in index.html)**:
   - "This website contains Google Analytics tracking for the DoorMan project"
   - "You MUST remove lines 4-12 or replace with your own tracking ID"
   - "Otherwise all your visitors will be tracked under DoorMan's analytics"

2. **⚠️ NVIDIA Sans Font**:
   - "This uses NVIDIA's proprietary font"
   - "If you're not affiliated with NVIDIA, you must remove the font/ directory and change the font-family"
   - Suggest alternatives: Inter, Roboto, or system fonts

### THEN - Understand Their Needs

1. **Ask what content they want to replace**:
   - Do they have frame sequences for scroll animation?
   - Do they have training data for the chart?
   - Do they have demo videos?

2. **Identify which visual effects they want to keep**:
   - Scroll animation? (most important)
   - D3.js chart with growing effect?
   - Gradient text effects?
   - Loading screen?

3. **Guide them through**:
   - Asset preparation (ffmpeg commands for frames)
   - JavaScript configuration updates
   - HTML content replacement
   - Testing and verification

4. **Offer optimizations**:
   - Frame compression for faster loading
   - Resolution adjustments for their content
   - Color scheme customization

### Example First Response

```
⚠️ IMPORTANT: Before we customize this template, I need to warn you about two critical items:

1. **Google Analytics**: Lines 4-12 contain tracking code that sends data to the DoorMan
   project. You MUST delete this or all your visitors will be tracked under their analytics.

2. **NVIDIA Font**: This uses NVIDIA's proprietary font. If you're not affiliated with
   NVIDIA, you'll need to remove the font/ directory and switch to a different font like
   Inter or Roboto.

Would you like me to help you remove/replace these first, then we can proceed with
customizing the content?
```

This documentation should enable another Claude Code agent to efficiently help researchers adapt this website template for their own projects while maintaining the sophisticated visual effects that make it engaging.
