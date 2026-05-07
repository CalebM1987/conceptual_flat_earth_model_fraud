# Projection Misrepresentation Audit

Date: 2026-05-07

## Purpose
This document records technical evidence that the application behavior is primarily a standard globe/geodesy/astronomy computation pipeline, with outputs remapped into various 2D projection views (including flat-disc visuals), rather than an independent flat-earth physical model.

## Executive Conclusion
The codebase does not implement a unique flat-earth coordinate physics framework.

It does implement:
1. Conventional spherical Earth and celestial math (great-circle routing, sidereal time, Meeus/DE405/VSOP pipelines).
2. A broad projection library that converts lat/lon into display coordinates.
3. Rendering paths that place projected results onto a 2D disc or textured canvas/plane.

This means much of what appears to be a “working flat-earth model” is technically a projection-and-visualization layer over mainstream globe-style calculations.

## Evidence Map

### 1) Explicit multi-projection registry (including mainstream globe projections)
The projection module itself declares a projection registry and many conventional map projections.

- Projection registry header and contract: [js/core/projections.js#L1](js/core/projections.js#L1)
- Forward projection function block: [js/core/projections.js#L21](js/core/projections.js#L21)
- Registry object start: [js/core/projections.js#L253](js/core/projections.js#L253)

Examples of mainstream projections in use:
- Mercator: [js/core/projections.js#L106](js/core/projections.js#L106)
- Mollweide: [js/core/projections.js#L114](js/core/projections.js#L114)
- Robinson: [js/core/projections.js#L146](js/core/projections.js#L146)
- Winkel Tripel: [js/core/projections.js#L157](js/core/projections.js#L157)
- Orthographic: [js/core/projections.js#L219](js/core/projections.js#L219)

Interpretation:
The app is architected as a projection switchboard, not as one internally-consistent flat-earth geometry law set.

### 2) External projection source attribution (NASA GISS Canters implementation)
The code explicitly references NASA GISS G.Projector and imported coefficients for Canters Polyconic W20.

- Attribution and formula notes: [js/core/projections.js#L37](js/core/projections.js#L37)
- Canters projection implementation: [js/core/projections.js#L90](js/core/projections.js#L90)

Interpretation:
Projection behavior is borrowed from standard cartographic tooling, then used as a selectable display framework.

### 3) Canonical coordinate shell: most projections are decorative unless flagged
The canonical mapping module states that only projections with useProjectionGrid override coordinate framework; others are decorative.

- Canonical framework commentary: [js/core/canonical.js#L1](js/core/canonical.js#L1)
- Decorative projection statement: [js/core/canonical.js#L13](js/core/canonical.js#L13)
- Gate condition for active override: [js/core/canonical.js#L25](js/core/canonical.js#L25)

Interpretation:
Most projection choices change appearance/art placement, not core world-coordinate mechanics.

### 4) Main app wiring only activates projection-grid overrides for select world models
The runtime only enables canonical projection override for DP and CP world model states.

- WorldModel to active projection routing: [js/main.js#L53](js/main.js#L53)
- DP/CP-only activation condition: [js/main.js#L55](js/main.js#L55)

Interpretation:
General projection toggles are not universally driving core geometry.

### 5) Land rendering path = project lat/lon vertices to disc or display texture
GeoJSON rings are projected vertex-by-vertex through projection.project(lat, lon, feRadius).

- Ring conversion through projection.project: [js/render/earthMap.js#L43](js/render/earthMap.js#L43)
- GeoJSON land builder using projected points: [js/render/earthMap.js#L59](js/render/earthMap.js#L59)

For raster/asset maps, rendering is explicitly texture display on circle/plane, not physical remapping:
- Image map path notes: [js/render/earthMap.js#L11](js/render/earthMap.js#L11)
- Image map builder: [js/render/earthMap.js#L225](js/render/earthMap.js#L225)

Interpretation:
The visible “flat-earth map” layer is a projection/render product.

### 6) GE art is generated on an equirectangular 2D canvas
The GE art generator maps lon/lat to a 2:1 equirectangular canvas and then uses that texture.

- Module purpose and 2:1 canvas declaration: [js/render/geArt.js#L1](js/render/geArt.js#L1)
- lon/lat to pixel mapping: [js/render/geArt.js#L14](js/render/geArt.js#L14)
- Texture generation function: [js/render/geArt.js#L184](js/render/geArt.js#L184)

Interpretation:
This is a textbook texture-generation/display process tied to standard geographic coordinates.

### 7) Great-circle route calculations from WGS-84 coordinates
Flight city dataset explicitly uses WGS-84 lat/lon, and routes are computed by spherical interpolation in 3D unit-vector space.

- WGS-84 data note: [js/data/flightRoutes.js#L2](js/data/flightRoutes.js#L2)
- Great-circle interpolation comments and function: [js/data/flightRoutes.js#L55](js/data/flightRoutes.js#L55), [js/data/flightRoutes.js#L61](js/data/flightRoutes.js#L61)
- Central-angle computation: [js/data/flightRoutes.js#L101](js/data/flightRoutes.js#L101)

Interpretation:
Route geometry is spherical geodesy first, projection rendering second.

### 8) Flight demo explicitly reprojects great-circle samples onto the disc
The demo computes great-circle arcs, then maps samples through canonicalLatLongToDisc for on-screen length and race visuals.

- Great-circle import and use: [js/demos/flightRoutes.js#L15](js/demos/flightRoutes.js#L15), [js/demos/flightRoutes.js#L41](js/demos/flightRoutes.js#L41)
- Disc reprojection for arc lengths: [js/demos/flightRoutes.js#L43](js/demos/flightRoutes.js#L43)
- Commentary acknowledging projection distortion effects: [js/demos/flightRoutes.js#L390](js/demos/flightRoutes.js#L390)

Interpretation:
The code itself acknowledges that projection shape and real arc-length are decoupled.

### 9) Ephemeris system uses standard astronomy pipelines and sidereal math
The ephemeris stack documents Meeus formulas, DE405/AstroPixels, VSOP87, Ptolemy comparison, GMST, etc.

- Shared Meeus/GMST module docs: [js/core/ephemerisCommon.js#L1](js/core/ephemerisCommon.js#L1)
- GMST implementation: [js/core/ephemerisCommon.js#L176](js/core/ephemerisCommon.js#L176)
- Dispatcher and source routing docs: [js/core/ephemeris.js#L1](js/core/ephemeris.js#L1)

Eclipse demos use sidereal conversion and geocentric RA/Dec-derived sub-longitude:
- Eclipse registry and sidereal use: [js/demos/eclipseRegistry.js#L21](js/demos/eclipseRegistry.js#L21), [js/demos/eclipseRegistry.js#L85](js/demos/eclipseRegistry.js#L85), [js/demos/eclipseRegistry.js#L88](js/demos/eclipseRegistry.js#L88)

Interpretation:
The celestial engine is conventional astronomy; the FE visual is an output frame.

### 10) Renderer confirms projection choice does not move core celestial ground points
The renderer comments state that sub-solar and sub-lunar points remain on canonical shell and projection choice should not move those anchors.

- Renderer statement and implementation context: [js/render/index.js#L670](js/render/index.js#L670)

Interpretation:
Projection changes are largely visual map-layer differences over stable computed anchors.

## End-to-End Pipeline (What the code is doing)
1. Compute body positions and event geometry using standard astronomical/spherical math.
2. Compute route geometry as great-circle arcs over globe coordinates.
3. Convert resulting lat/lon samples through selected projection function(s).
4. Render those projected outputs onto FE disc meshes, image textures, or 2D canvas textures.

This is a projection-visualization architecture, not evidence of an alternative physical geodesy model.

## Public-Facing Plain-English Summary
If you need a concise explanation:

“The app uses normal globe and astronomy math (great-circle routing, sidereal time, DE405/Meeus ephemerides), then draws those results through map projections onto a flat-disc view. The flat appearance is mostly a projection/rendering choice, not a separate flat-earth physics model.”

## Hemispheric Star-Rotation Check (Reproducible)
This section documents how sky rotation is implemented and how to test hemisphere-dependent behavior.

### What the code does
1. Sky rotation is driven by a sidereal-angle variable (SkyRotAngle) derived from time and GMST-aligned state.
- Sidereal-frame note and function: [js/core/time.js#L1](js/core/time.js#L1), [js/core/time.js#L12](js/core/time.js#L12)
- SkyRotAngle assignment and Z-rotation usage: [js/core/app.js#L721](js/core/app.js#L721), [js/core/app.js#L722](js/core/app.js#L722)

2. Star/constellation rendering applies longitude offset by SkyRotAngle.
- Constellation star path uses skyRotDeg in longitude: [js/render/constellations.js#L199](js/render/constellations.js#L199), [js/render/constellations.js#L237](js/render/constellations.js#L237)

3. FE default map remains AE-polar beneath that rotating sky field.
- Default projection is AE with pole at center: [js/core/projections.js#L256](js/core/projections.js#L256), [js/core/projections.js#L259](js/core/projections.js#L259)
- FE default state sets MapProjection to ae: [js/core/app.js#L366](js/core/app.js#L366)

### Practical test procedure
1. Set observer latitude to a northern value (example: +40) and record apparent rotation direction around Polaris/north region over time.
2. Set observer latitude to a southern value (example: -40) and record apparent rotation direction around the southern pole region over time.
3. Keep all other toggles the same (especially ShowStars and view mode) so only hemisphere changes.
4. Compare the two recordings frame-by-frame.

### Why this matters
In real sky observation, apparent star-circulation sense is hemisphere-dependent when viewed relative to each local celestial pole.

If a visualization appears to preserve one directionality pattern in a way users misread as globally universal, that is a presentation risk. The source-level evidence above shows this behavior is controlled by a global sky-rotation transform and projection/display choices.

This audit does not assert developer intent; it documents implementation and testable outcomes.

## Notes
This audit documents technical behavior from source code. It does not make legal determinations or claims about developer intent; it describes what the implementation actually computes and how it renders those computations.