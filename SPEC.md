# Golden Gate Bridge 3D Voxel Simulation - Specification

## 1. Project Overview
- **Project Name**: Golden Gate Bridge 3D Voxel Simulation
- **Type**: Interactive 3D WebGL visualization
- **Core Functionality**: High-fidelity 3D voxel-style Golden Gate Bridge with realistic lighting, water, traffic, ships, birds, and day/night cycle
- **Target Users**: Anyone wanting an immersive 3D Golden Gate Bridge experience

## 2. Visual & Rendering Specification

### Scene Setup
- **Camera**: Perspective camera with orbit controls (restricted to prevent underground view)
  - Initial position: Elevated view from Marin side
  - Zoom range: 50-500 units
  - Vertical rotation: 10° - 85° (prevent flipping)
- **Background**: Dynamic sky gradient based on time-of-day
- **Coordinate System**: Y-up, bridge along X-axis

### Lighting System
- **Time-of-Day Control** (0-24h slider):
  - Sun position calculated from time (arc path across sky)
  - Sun intensity: 0 at night (6PM-6AM), peak at noon (1.5)
  - Ambient light: 0.1 (night) to 0.4 (day)
  - Sky color interpolation:
    - Night: #0a0a1a (deep blue-black)
    - Dawn/Dusk: #ff6b35 → #ff9966 (orange gradient)
    - Day: #87CEEB (sky blue)
    - Sunset: #ff4444 → #ff8800
- **Bridge Lights**: Warm yellow (#ffaa44) point lights along bridge
- **City Lights**: Scattered warm white point lights on SF side (night only)

### Fog System
- **Type**: Exponential squared fog (THREE.FogExp2)
- **Density Slider**: 0-100 maps to 0.0-0.015
- **Color**: Tinted based on time-of-day (warmer at sunrise/sunset)
- **Volumetric Effect**: Sprite particle system for atmospheric haze

### Water Shader
- **Geometry**: Large plane (2000x2000 units) with 256x256 segments
- **Custom Shader Features**:
  - Gerstner waves (3 wave layers)
  - Fresnel reflection (environment map)
  - Specular highlights from sun
  - Depth-based color gradient (shallow: #1a5f7a, deep: #0a2a3f)
  - Distance fog blending (exp2)
- **Animation**: Time-based wave displacement

### Post-Processing
- **Tone Mapping**: ACESFilmicToneMapping
- **Bloom**:
  - Threshold: 0.8
  - Strength: 0.5 (day) / 1.2 (night)
  - Radius: 0.4
- **Exposure**: 1.0

## 3. 3D Assets Specification

### Golden Gate Bridge (Voxel-Style)
- **Towers** (2x):
  - Height: ~230 units (scaled)
  - Art-deco style with stepped sections
  - International Orange color (#C0362C)
  - Cross-bracing details
- **Main Span**: ~1280 units length
- **Deck**:
  - Width: 27 units
  - Road lanes (6 lanes implied)
  - Sidewalk areas
- **Cables**:
  - Main cable: Catenary curve between towers
  - Suspender cables: Vertical lines from main cable to deck
- **Piers**: 2 supporting piers in water
- **Anchors**: Large concrete blocks at each end

### Terrain
- **Marin Headlands**:
  - Procedural heightmap on north side
  - Rolling hills silhouette
  - Green-brown vegetation colors
- **SF Peninsula**:
  - South side with city blocks
  - Flat to hilly transition

### City (SF Side)
- **Building Blocks**: Instanced box geometries
- **Count**: ~200 buildings
- **Height Variation**: 10-80 units
- **Colors**: Gray concrete tones with lit windows at night

### Traffic (Cars)
- **Implementation**: InstancedMesh
- **Max Count**: 400 vehicles
- **Density Slider**: 0-100 maps to 0-400 cars
- **Vehicle Types**:
  - Sedans (small boxes)
  - Trucks (longer boxes)
- **Colors**: Random from palette
- **Headlights/Taillights**: Emissive materials at night
- **Movement**: Follow deck lanes, loop around

### Ships
- **Count**: 3-5 cargo ships
- **Geometry**: Procedural hull + container stacks
- **Navigation Lights**:
  - Red (port) - left side
  - Green (starboard) - right side
  - White (mast) - top
- **Movement**: Slow transit across bay

### Birds (Flocking)
- **Count**: 50 birds
- **Implementation**: InstancedMesh (small cones/planes)
- **Behavior**: Boids algorithm (separation, alignment, cohesion)
- **Animation**: Wing flapping via shader

## 4. Interaction Specification

### UI Controls (Overlay Panel)
- **Position**: Top-right corner, dark semi-transparent panel
- **Sliders**:
  1. **Time of Day** (0-24, default 14)
     - Label shows: "Time: 14:00"
  2. **Fog Density** (0-100, default 30)
     - Label shows: "Fog: 30%"
  3. **Traffic Density** (0-100, default 50)
     - Label shows: "Traffic: 50%"
  4. **Camera Zoom** (50-500, default 300)
     - Label shows: "Zoom: 300"
- **Style**:
  - Dark background (#1a1a2e, 80% opacity)
  - Rounded corners (8px)
  - White text, accent color (#ff6b35)
  - Custom styled range inputs

### Camera Controls
- **Orbit**: Left mouse drag
- **Zoom**: Mouse wheel
- **Pan**: Right mouse drag (disabled to keep focus on bridge)

## 5. Performance Targets

- **Target FPS**: 60 FPS on mid-range GPU
- **Draw Calls**: < 50
- **Triangles**: < 500k
- **Optimization Techniques**:
  - InstancedMesh for cars, birds, buildings
  - LOD not needed (fixed camera range)
  - Efficient fog (no particles at distance)
  - Shared materials

## 6. Acceptance Criteria

1. ✅ Bridge renders with recognizable art-deco towers and cables
2. ✅ Time slider changes sky color, sun position, lighting
3. ✅ Fog slider affects visibility distance
4. ✅ Water has animated waves and reflections
5. ✅ Cars drive across bridge, count matches slider
6. ✅ Ships move across bay with nav lights
7. ✅ Birds flock naturally
8. ✅ Night mode shows all lights (city, bridge, cars, ships)
9. ✅ Bloom effect visible on lights at night
10. ✅ UI controls responsive and styled
11. ✅ Smooth 60 FPS performance
12. ✅ Single HTML file runs in Chrome
