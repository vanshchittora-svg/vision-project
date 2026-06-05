**PROJECT REQUIREMENTS DOCUMENT**

**AR/VR Injection Precision Trainer**

**Platform:** Pico 4 Ultra (AR/VR Headset)

**Primary Tools:** Unity 2022.3 LTS, Blender 4.x

**Project Duration:** 3 Weeks

# 1\. Project Overview

## 1.1 Purpose

This document defines the functional, technical, and design requirements for an augmented reality / virtual reality injection precision training simulator. The application runs on the Pico 4 standalone headset and is designed to help trainees practice intravenous (IV) injection technique with real-time AR guidance overlaid onto their own hand.

The simulator provides measurable feedback on needle angle, entry point accuracy, and insertion depth - enabling safe, repeatable practice without clinical equipment or risk to patients.

# 2\. Functional Requirements

## 2.1 AR Pass Through Overlay

- The application shall render live colour pass through from the Pico 4 cameras as the background layer.
- An anatomical vein map overlay shall be projected onto the tracked hand surface, highlighting safe entry zones in green and high-risk zones in red.
- A real-time angle indicator HUD shall display the current needle angle in degrees relative to the skin surface.
- The overlay anchor shall be locked to the wrist joint to maintain stability under hand movement.
- Overlay opacity shall be adjustable between 30% and 100% via a slider in the settings panel.

## 2.2 Injection Scoring

### 2.2.1 Scoring Criteria

| **Parameter**        | **Ideal Range**    | **Acceptable Range** |
| -------------------- | ------------------ | -------------------- |
| Needle entry angle   | 15° - 25°          | 10° - 35°            |
| Entry point accuracy | Within vein region | Within 5 mm of vein  |
| Insertion depth      | 3 - 5 mm           | 2 - 7 mm             |

## 2.3 UI & Navigation

- Main Menu: Start Training, Tutorial, Settings, Quit.
- Tutorial Mode: 5-step guided walkthrough of correct technique, skippable.
- Training Mode: live AR session with HUD.
- Results Screen: composite score, parameter breakdown, Retry and Main Menu buttons.
- Settings Panel: overlay opacity slider, dominant hand selection (left/right).
- All UI shall be rendered in world space using Unity's XR UI Toolkit.

# 3\. Non-Functional Requirements

| **Category** | **Requirement**            | **Target**                                                       |
| ------------ | -------------------------- | ---------------------------------------------------------------- |
| Performance  | Sustained frame rate       | \>= 60 fps in all scenes                                         |
| Performance  | Hand tracking latency      | < 15 ms end-to-end                                               |
| Performance  | App launch to main menu    | < 8 seconds                                                      |
| Rendering    | Total polygon budget       | < 30,000 triangles (all visible meshes)                          |
| Rendering    | Texture memory             | < 64 MB total                                                    |
| Stability    | Session crash rate         | 0 crashes in 30-minute session                                   |
| Usability    | Onboarding time (new user) | < 3 minutes to first scored attempt                              |
| Comfort      | Cybersickness mitigation   | No artificial locomotion; passthrough anchors user to real space |
| Battery      | Session battery draw       | < 15% per 30-minute session                                      |
| Build        | APK size                   | < 500 MB                                                         |

# 4\. Technical Requirements

## 4.1 Hardware

| **Component**      | **Specification**                                         |
| ------------------ | --------------------------------------------------------- |
| Target Device      | Pico 4 ultra standalone headset                           |
| OS                 | PicoOS 5.x or later                                       |
| Hand Tracking      | Pico 4 built-in optical hand tracking (non-dominant hand) |
| Passthrough Camera | Pico 4 colour stereo passthrough                          |

## 4.2 Software & SDKs

| **Tool / SDK**                  | **Version**   | **Purpose**                                      |
| ------------------------------- | ------------- | ------------------------------------------------ |
| Unity                           | 2022.3 LTS    | Primary development environment                  |
| PICO Unity Integration SDK      | v3.x          | Pico 4 device access, passthrough, hand tracking |
| Universal Render Pipeline (URP) | 14.x (Mobile) | Rendering pipeline optimised for standalone VR   |
| Blender                         | 5.1           | 3D asset creation (hand, syringe, environment)   |
| XR Interaction Toolkit          | 2.x           | XR rig, interactable framework                   |
| Unity XR Plugin Management      | 4.x           | XR provider configuration                        |
| TextMeshPro                     | Bundled       | World-space UI text rendering                    |

## 4.3 3D Asset Requirements

| **Asset**          | **Poly Budget** | **Texture Resolution** | **Format**   |
| ------------------ | --------------- | ---------------------- | ------------ |
| Hand model (full)  | 12,000 tris     | 2048 x 2048 px         | FBX          |
| Syringe & needle   | 4,000 tris      | 1024 x 1024 px         | FBX          |
| Environment (room) | 6,000 tris      | 1024 x 1024 px         | FBX          |
| UI plane meshes    | < 500 tris      | N/A                    | Unity native |

## 4.4 AR Overlay Technical Approach

- Passthrough layer rendered via Pico SDK PXR_PassThrough API.
- AR overlays rendered as Unity canvas objects in world space, parented to wrist anchor.
- Vein map: static texture mask projected via Unity's projector system onto hand mesh.
- Angle indicator: computed from Vector3.Angle(needleForward, handSurfaceNormal) updated each frame.
- Collision for entry detection: MeshCollider on hand mesh + Raycast from needle tip transform.

# 5\. Risks & Mitigations

| **Risk**                                              | **Likelihood** | **Impact** | **Mitigation**                                                  |
| ----------------------------------------------------- | -------------- | ---------- | --------------------------------------------------------------- |
| Hand tracking instability in varied lighting          | Medium         | High       | Test early (Week 1); add fallback controller-only mode          |
| Passthrough API breaking changes between SDK versions | Low            | High       | Pin SDK version; test passthrough on Day 1                      |
| Performance below 60 fps due to skin shader           | Medium         | High       | Build lightweight fallback shader; profile in Week 2            |
| Blender export / Unity import material issues         | Medium         | Medium     | Export test asset Week 1 Day 1 to catch pipeline issues early   |
| Scope creep beyond 3-week timeline                    | High           | Medium     | All Phase 3 features are P2 (nice-to-have); core scoring is P0  |
| Medical accuracy of vein placement questioned         | Low            | Medium     | Reference anatomical atlas; note training-aid disclaimer in app |

# 6\. Acceptance Criteria

The project will be considered complete and ready for demonstration when all of the following are verified on the physical Pico 4 device:

| **#** | **Criterion**                                                                 | **Verification Method**          |
| ----- | ----------------------------------------------------------------------------- | -------------------------------- |
| AC-01 | Application launches and reaches main menu in < 8 seconds                     | Stopwatch test x3                |
| AC-02 | Hand tracking detected and overlay renders within 3 seconds of hand placement | Tester observation               |
| AC-03 | Vein overlay remains stable on wrist anchor during normal hand movement       | Video review                     |
| AC-04 | Needle entry correctly detected and angle captured within ±2°                 | Manual angle comparison          |
| AC-05 | Composite score calculated and displayed on results screen after each attempt | 5 sequential attempts            |
| AC-06 | Haptic feedback fires on needle entry (when enabled)                          | Tester confirms tactile response |
| AC-07 | Application runs at >= 90 fps for a continuous 10-minute session              | Unity stats overlay              |
| AC-08 | APK size < 500 MB                                                             | File size check                  |
| AC-09 | Tutorial mode completable in < 3 minutes by first-time user                   | Timed user test                  |
| AC-10 | Zero crashes in 30-minute continuous demo session                             | Live demo run-through            |

# 7\. Glossary

| **Term**                 | **Definition**                                                                                                               |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| AR (Augmented Reality)   | Digital overlays composited onto a real-world camera feed                                                                    |
| VR (Virtual Reality)     | Fully immersive synthetic environment replacing real-world view                                                              |
| Passthrough              | Pico 4 feature rendering live colour camera feed inside the headset, enabling AR                                             |
| Pico 4                   | Standalone XR headset by PICO Technology; supports 6-DoF tracking, hand tracking, and colour passthrough                     |
| Unity URP                | Universal Render Pipeline - Unity's scriptable rendering pipeline optimised for mobile and standalone XR                     |
| XR Rig                   | Unity GameObject hierarchy representing the player's tracked head and hands in XR space                                      |
| DoF (Degrees of Freedom) | Number of independent axes of movement tracked (6-DoF = position + rotation in 3D space)                                     |
| FBX                      | Autodesk file format for exchanging 3D models between Blender and Unity                                                      |
| ASTC                     | Adaptive Scalable Texture Compression - format used by Pico 4's mobile GPU for efficient texture memory                      |
| PBR                      | Physically Based Rendering - material system simulating real-world light interaction (base colour, roughness, metallic maps) |
| IV Injection             | Intravenous injection - administration of fluid directly into a vein; primary clinical technique this simulator trains       |
| LOD                      | Level of Detail - technique reducing polygon count on distant objects to save GPU performance                                |
| APK                      | Android application package - the deployable binary sideloaded onto the Pico 4                                               |

_End of Document_
