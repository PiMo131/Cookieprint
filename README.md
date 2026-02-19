# 🍪 CookieCut

**Photo → Contour → STL → 3D Print**

CookieCut is a single-file browser app that turns any photo or line drawing into a 3D-printable cookie cutter STL — no installation, no server, no dependencies to install. Just open the HTML file and go.

---
Can't wait to start baking? Check out the working app here: https://pimo131.github.io/Cookieprint/
---

<img width="1889" height="785" alt="image" src="https://github.com/user-attachments/assets/5107dc5a-c5cd-4405-ba5d-e68fde1791cf" />

<img width="1625" height="740" alt="image" src="https://github.com/user-attachments/assets/b134b5b3-c098-414f-ac32-680143e744ac" />



## Demo

| Step | What happens |
|------|-------------|
| Upload | Load a photo or line drawing |
| Reference | Click 2 points, enter their real-world distance in mm → automatic px/mm scale |
| Trace | Marching Squares auto-detects the contour, or draw manually point-by-point |
| Configure | Set wall thickness, cutter height, optional flange |
| Export | Download binary STL, open in Cura / PrusaSlicer / Bambu Studio |

---

## Features

### Contour Detection — Marching Squares
- Gaussian blur → binary threshold (adjustable) → flood-fill background removal → Marching Squares iso-contour → Ramer-Douglas-Peucker simplification → Chaikin smoothing
- Works on photos, hand-drawn sketches, logos, and silhouettes
- Adjustable threshold slider for tricky images (dark-on-light and light-on-dark both supported)

### Manual Draw Mode
- Click to place points, Backspace to undo last point
- Snap indicator when closing the loop
- Press Enter or double-click to close — or just click near the start point
- Esc to cancel

### Polygon Editing
- Drag any vertex to reposition
- Click on an edge to insert a new vertex
- Double-click a vertex to remove it
- "Smooth" applies Chaikin subdivision, "Simplify" runs another RDP pass

### Self-Intersection Detection & Cleanup
- Every edit is checked for crossing edges
- Count shown live in the info panel
- One-click cleanup: iteratively resolves each crossing by splitting the polygon and keeping the larger sub-polygon

### 3D Preview (Three.js)
- Real-time orbit, pan, and zoom
- Amber key light + cyan fill + soft shadows
- Available after step 4 — no need to download the STL first

### STL Generation
- Binary STL with correct face normals (cross product per triangle)
- Wall geometry: inner wall, outer wall, top ring, bottom ring
- Optional flange: 3× wall thickness wide, 2× wall thickness tall, added at the top
- Polygon offset via vertex bisector method with miter clamping (handles convex and concave corners)

---

## Usage
###easy mode
```
1. go to the link in the top for the live website mode
```
###locally running
```
1. Download cookiecut-v2.html
2. Open in any modern browser (Chrome, Firefox, Safari, Edge)
3. Follow the 5 steps in the sidebar
4. Download the STL and slice it
```

No npm, no build step, no internet connection required after the file is loaded.

> **Tip:** Dark lines on a light background give the best auto-trace results. For complex photos, lower the threshold slider until the shape fills solid, then click Auto-Trace.

---

## Recommended Print Settings

| Setting | Value |
|---------|-------|
| Material | PETG or PLA |
| Layer height | 0.2 mm |
| Wall thickness | Match your app setting (default 2 mm) |
| Infill | 15–20% |
| Supports | Not needed |
| Brim | Recommended for tall cutters |

The cutter is designed to stand on its flat bottom (the cutting edge faces down into the dough).

---

## Parameters Explained

| Parameter | What it does |
|-----------|-------------|
| **Wall thickness** | Thickness of the cutting ring in mm |
| **Cutter height** | How tall the cutting blade is in mm |
| **Flange** | Adds a wide flat rim at the top: 3× wall thick outward, 2× wall thick tall. Gives you a comfortable surface to press down on. |

---

## Technical Notes

**Marching Squares** generates a contour by classifying each 2×2 pixel cell as inside/outside and placing a line segment across the boundary. This produces smooth, continuous contours compared to pixel-walking approaches. The result is stitched into chains, then reduced with RDP.

**Polygon offset** for wall thickness uses the angle bisector at each vertex. The miter distance is clamped to 6× the offset distance to prevent spikes at sharp concave corners.

**Self-intersection cleanup** uses a segment-segment intersection test on all non-adjacent edge pairs. When a crossing is found, the polygon is split at the intersection point into two sub-polygons; the one with the larger signed area is kept. This is repeated until no crossings remain.

**STL normals** are computed per-triangle using the cross product of two edges, normalized. The winding order follows the right-hand rule: CCW when viewed from outside the solid.

---

## Browser Compatibility

| Browser | Status |
|---------|--------|
| Chrome 90+ | ✅ Full support |
| Firefox 88+ | ✅ Full support |
| Safari 15+ | ✅ Full support |
| Edge 90+ | ✅ Full support |
| Mobile (iOS/Android) | ⚠️ Works, but editing is awkward without a mouse |

---

## File Structure

```
cookiecut-v2.html   ← Everything. One file.
```

The Three.js library is loaded from the Cloudflare CDN (`cdnjs.cloudflare.com`). If you need offline use, download Three.js r128 and replace the `<script src>` tag with a local path.

---

## Known Limitations

- Very complex shapes (many tight concave corners) can produce imperfect offsets
- The manual draw mode doesn't support curved segments — all edges are straight lines
- No undo history beyond the current polygon state (Backspace only works during manual draw)
- STL has no bottom face on the interior (open bottom is intentional for a cookie cutter)

---

## License

MIT — do whatever you want, print as many cookies as you like.
