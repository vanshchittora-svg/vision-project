**DESIGN DOCUMENT**

**AR/VR Injection Precision Trainer**

A standalone augmented reality simulator for intravenous injection training on the Pico 4 Ultra headset, providing real-time AR overlay guidance and precision scoring.

| **Platform** | **Engine**       | **3D Tools** | **Duration** |
| ------------ | ---------------- | ------------ | ------------ |
| Pico 4 Ultra | Unity 2022.3 LTS | Blender 5.1  | 3 Weeks      |

# 1\. Project Overview

This document defines the functional, technical, and design requirements for an augmented reality / virtual reality injection precision training simulator. The application runs on the Pico 4 standalone headset and is designed to help trainees practise intravenous (IV) injection technique with real-time AR guidance overlaid onto their own hand.

The simulator provides measurable feedback on needle angle, entry point accuracy, and insertion depth - enabling safe, repeatable practice without clinical equipment or risk to patients.

# 2\. Functional Requirements

## 2.1 AR Passthrough Overlay

The application renders live colour passthrough from the Pico 4 cameras as the background layer. The following overlays are composited on top:

- Anatomical vein map projected onto the tracked hand surface - safe entry zones highlighted in green, high-risk zones in red.
- Real-time angle indicator HUD displaying current needle angle in degrees relative to the skin surface.
- Overlay anchor locked to the wrist joint for stability under hand movement.
- Overlay opacity adjustable between 30% and 100% via a slider in the settings panel.

## 2.2 Injection criteria

Scoring is based on three parameters measured at the moment of needle entry:

| **Parameter**        | **Ideal Range**    | **Acceptable Range** |
| -------------------- | ------------------ | -------------------- |
| Needle entry angle   | 15° - 25°          | 10° - 35°            |
| Entry point accuracy | Within vein region | Within 5 mm of vein  |
| Insertion depth      | 3 - 5 mm           | 2 - 7 mm             |

## 2.3 UI & Navigation

All UI is rendered in world space using Unity's XR UI Toolkit. The application comprises five screens:

- Main Menu - Start Training, Tutorial, Settings, Quit.
- Tutorial Mode - 5-step guided walkthrough of correct technique, skippable.
- Training Mode - live AR session with HUD active.
- Results Screen - composite score, parameter breakdown, Retry and Main Menu buttons.
- Settings Panel - overlay opacity slider, dominant hand selection (left/right).

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

## AR Overlay Technical Approach

- Passthrough layer rendered via Pico SDK PXR_PassThrough API.
- AR overlays rendered as Unity canvas objects in world space, parented to wrist anchor.
- Vein map: static texture mask projected via Unity's projector system onto hand mesh.
- Angle indicator: computed from Vector3.Angle(needleForward, handSurfaceNormal) updated each frame.
- Collision for entry detection: MeshCollider on hand mesh + Raycast from needle tip transform.

_- End of Document -_
