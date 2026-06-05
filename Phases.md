# Three-Phase Development Plan

## Phase 1 - Foundation & Asset Creation (Week 1, Days 1-7)

### Blender Tasks

- Model anatomically accurate hand (bones, vein topology, skin layers)
- UV unwrap all assets; bake AO and normal maps
- Create PBR texture sets: skin (base colour, roughness, SSS mask), metal, plastic
- Export all assets as FBX with embedded textures

### Unity Tasks

- Create Unity project with URP (Mobile) and Pico SDK configured
- Verify hand tracking and passthrough on physical Pico 4 device
- Import Blender assets; configure materials for URP Lit shader
- Set up XR Rig with controller-tracked syringe in dominant hand
- Basic scene hierarchy: Main Camera, XR Rig, HandAnchor, SyringeRoot, UICanvas

### Week 1 Deliverable

Hands imported and rendering correctly. Application boots and runs on Pico 4. Hand and syringe visible in headset. Hand tracking confirmed functional.

## Phase 2 - Interaction & Simulation Logic (Week 2, Days 8-14)

### Blender Tasks

- Model syringe assembly (barrel, plunger, needle) as separate rigged objects
- UV unwrap all assets; bake AO and normal maps

### Injection Mechanics

- Implement raycasting from needle tip; detect entry on hand mesh collider
- Capture angle, entry point, and depth at moment of contact
- Implement scoring algorithm (see Section 3.4)
- Skin deformation: vertex displacement shader pushing geometry inward at contact point
- AR Guidance System
- Integrate Pico pass through API; confirm colour pass through renders correctly
- Build wrist anchor transform; attach overlay root to wrist joint
- Implement vein highlight overlay with safe / danger zone colouring
- Implement session pause / resume on hand tracking loss

### Week 2 Deliverable

Full injection loop functional: user holds syringe, approaches hand, needle entry detected, score calculated. AR overlay tracks hand in pass through view.

## Phase 3 - Polish, Scoring UI & Demo Prep (Week 3, Days 15-21)

### UI & UX

- Build Main Menu, Tutorial (5 steps), Training Mode, Results Screen, Settings Panel
- Implement session score history.
- Add celebration particle effect on perfect score
- Overlay opacity and haptic toggle in settings

### Performance Optimisation

- Profile on-device using Unity GPU Profiler; target sustained 60 fps
- Apply LOD group to hand model (2 LOD levels)
- Enable occlusion culling in scene settings
- Compress all textures to ASTC format for Pico GPU

### Demo Preparation

- Build final APK; sideload via ADB or SideQuest
- Conduct minimum 3-user test session; log critical bugs
- Fix P0 and P1 bugs; document known issues
- Prepare 5-minute demo script and backup APK

### Week 3 Deliverable

Polished, stable APK sideloaded on Pico 4. All core features functional. Application runs at 60 fps. Ready for project demonstration.
