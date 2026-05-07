# Projection Misrepresentation Receipts (One-Page)

Date: 2026-05-07

## Core Claim
The app primarily computes globe/astronomy outputs, then projects or textures them into flat-disc visuals.

## Fast Receipts (Claim -> Code)

1. Default FE map is north-pole azimuthal-equidistant (AE), not a novel flat-earth coordinate law.
- Default AE entry: [js/core/projections.js#L256](js/core/projections.js#L256)
- AE note ("pole at disc centre"): [js/core/projections.js#L259](js/core/projections.js#L259)

2. Canonical disc mapping is AE by default.
- canonicalLatLongToDisc fallback formula: [js/core/canonical.js#L28](js/core/canonical.js#L28)

3. Most projection options are decorative unless useProjectionGrid is enabled.
- Decorative-projection statement: [js/core/canonical.js#L13](js/core/canonical.js#L13)
- Gate for override: [js/core/canonical.js#L25](js/core/canonical.js#L25)

4. Runtime only activates projection-grid overrides for special world models (DP/CP).
- World model routing: [js/main.js#L53](js/main.js#L53)
- DP/CP activation: [js/main.js#L55](js/main.js#L55)

5. GeoJSON land gets projected point-by-point from lat/lon to disc coordinates.
- Ring projection call: [js/render/earthMap.js#L43](js/render/earthMap.js#L43)
- GeoJSON build path: [js/render/earthMap.js#L59](js/render/earthMap.js#L59)

6. Image maps are display textures on circles/planes (visual layer).
- Image-path description: [js/render/earthMap.js#L11](js/render/earthMap.js#L11)
- Image map builder: [js/render/earthMap.js#L225](js/render/earthMap.js#L225)

7. GE art textures are generated on a 2D equirectangular canvas from lon/lat.
- 2:1 canvas and mapping: [js/render/geArt.js#L9](js/render/geArt.js#L9), [js/render/geArt.js#L14](js/render/geArt.js#L14)
- Texture generation: [js/render/geArt.js#L184](js/render/geArt.js#L184)

8. Flight routes use WGS-84 coordinates and great-circle spherical interpolation.
- WGS-84 city data note: [js/data/flightRoutes.js#L2](js/data/flightRoutes.js#L2)
- Great-circle math: [js/data/flightRoutes.js#L55](js/data/flightRoutes.js#L55), [js/data/flightRoutes.js#L61](js/data/flightRoutes.js#L61)

9. Flight demos then reproject those spherical arcs onto the FE disc.
- Great-circle sampling in demo: [js/demos/flightRoutes.js#L41](js/demos/flightRoutes.js#L41)
- Disc projection usage: [js/demos/flightRoutes.js#L43](js/demos/flightRoutes.js#L43)

10. Ephemeris stack is standard astronomy (Meeus/DE405/VSOP/GMST), not a custom FE celestial physics engine.
- Shared ephemeris/GMST docs: [js/core/ephemerisCommon.js#L1](js/core/ephemerisCommon.js#L1)
- GMST function: [js/core/ephemerisCommon.js#L176](js/core/ephemerisCommon.js#L176)
- Dispatcher/pipeline docs: [js/core/ephemeris.js#L1](js/core/ephemeris.js#L1)

11. Sky rotation is tied to sidereal angle and applied as Z-rotation.
- SkyRotAngle computed from GMST: [js/core/app.js#L721](js/core/app.js#L721)
- Rotation matrix usage: [js/core/app.js#L722](js/core/app.js#L722)
- Time model explicitly says sidereal period: [js/core/time.js#L1](js/core/time.js#L1)

12. Star rendering applies SkyRotAngle-driven longitude shift.
- Constellation/star rotation handling: [js/render/constellations.js#L199](js/render/constellations.js#L199), [js/render/constellations.js#L237](js/render/constellations.js#L237)

## Bottom Line
Observed behavior matches a projection-and-visualization system layered over mainstream spherical/geocentric astronomical computation, not an independent flat-earth mechanics model.