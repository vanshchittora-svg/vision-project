Software Architecture Description (SAD): AR/VR Injection Precision Trainer
==========================================================================

1\. Introduction & Architectural Goals
--------------------------------------

### 1.1 Purpose

This document provides a high-level architectural overview of the AR/VR Injection Precision Trainer application. It translates the project's functional requirements into a concrete technical implementation plan for a standalone XR headset deployment.

### 1.2 System Objectives

The core architectural focus is to deliver an interactive training loop utilizing spatial hardware APIs with minimal latency.

```
                                  +-----------------------+
                                  |   Pico Standalone OS  |
                                  +-----------+-----------+
                                              |
                                              v
                                  +-----------------------+
                                  | PICO Unity SDK / XR   |
                                  +-----------+-----------+
                                              |
                                              v
+-----------------------+         +-----------+-----------+         +-----------------------+
|   3D Asset Pipeline   | ------> |  Unity XR Application | <-----+ |  Interaction System   |
| (Hand & Syringe FBX)  |         +-----------+-----------+         | (Raycast & Colliders) |
+-----------------------+                     |                     +-----------------------+
                                              v
                                  +-----------------------+
                                  |   World-Space UI /    |
                                  |   Feedback Systems    |
                                  +-----------------------+

```

### 1.3 Architectural Design Principles

-   **Performance-First Modularity:** Separate computationally heavy operations (such as mathematical angle calculations and collision evaluations) from rendering presentation logic to safeguard a consistent frame rate.

-   **Hardware Abstraction:** Leverage standardized OpenXR and vendor-specific subsystems so that internal tracking data flows predictably into application components.

-   **Deterministic Simulation Flow:** Structure the injection lifecycle around strict structural phases---State Setup, Contact Evaluation, Scoring Calculation, and Reset---to eliminate unpredictable race conditions during physical interaction.

2\. High-Level Subsystems & Component Topology
----------------------------------------------

The system is divided into four main structural subsystems executing concurrently within the Unity engine runtime environment:

```
+---------------------------------------------------------------------------------------+
|                                     UNITY ENGINE                                      |
|                                                                                       |
|  +-------------------------+                           +---------------------------+  |
|  |    Hardware Interface   |                           |    Core Simulation        |  |
|  |                         |                           |                           |  |
|  |  [PXR_PassThrough API]  | --(Camera Frame Stream)-> |  [Skin Deformation]       |  |
|  |  [Optical Hand Tracking]| --(Wrist Joint Transform) |  [Needle Raycast System]  |  |
|  |  [XR Interaction Toolkit|                           |  [Scoring Engine]         |  |
|  +-------------------------+                           +---------------------------+  |
|               |                                                      |                |
|               | (Tracking Datastream)                                | (State Changes)|
|               v                                                      v                |
|  +-------------------------+                           +---------------------------+  |
|  |   Scene Graph / Rig     |                           |    Presentation / UI      |  |
|  |                         |                           |                           |  |
|  |  [Main Camera]          |                           |  [World-Space Canvas]     |  |
|  |  [XR Rig / Wrist Joint] |                           |  [Real-Time Angle HUD]    |  |
|  |  [Syringe Root Object]  |                           |  [Results Dashboard]      |  |
|  +-------------------------+                           +---------------------------+  |
+---------------------------------------------------------------------------------------+

```

### 2.1 Hardware Interface Layer

-   **Pico OS Wrappers:** Abstracts low-level camera and sensor data. It streams real-time background frames and processes raw hardware positions into standard spatial coordinate transformations.

-   **Input Routing Subsystem:** Translates optical hand joint configurations and primary controller orientation matrices into structured vectors readable by scene elements.

### 2.2 Scene Graph & Rig Topology

-   **XR Rig Core:** Manages the active viewpoint positioning and spatial origins inside the simulation space.

-   **Dynamic Spatial Anchors:** Contains dedicated nodes that match the positional updates of the tracked human joints and physical tools. This acts as the structural point of origin for virtual overlays.

### 2.3 Core Simulation & Computation Engine

-   **Spatial Intersection System:** Evaluates geometric collisions between interactable items using real-time spatial casting methods.

-   **Mathematical Processing Engine:** Continuously measures angular discrepancies and depths relative to surface boundaries at the point of impact.

-   **Deformation Shader Controller:** Modifies mesh surface vector positions dynamically to provide physical feedback upon object contact.

### 2.4 Presentation & UI Subsystem

-   **Spatial Interface Layouts:** Generates user screens directly in 3D world space, utilizing specific formatting modules designed for immersive readability.

-   **Real-time HUD Systems:** Updates text fields and dynamic gauges based on mathematical outputs from active tracking engines.

3\. Data Flow & State Lifecycles
--------------------------------

### 3.1 Frame Lifecycle & Pass-Through Pipeline

Every execution frame processes camera images and sensor coordinates sequentially to guarantee minimal visual lag.

```
 [ Pico Hardware Cameras ]
             |
             | (Capture Environment Frame)
             v
 [ PXR_PassThrough API Layer ]
             |
             | (Render Background Layer)
             v
 [ Optical Joint Sensor Array ]
             |
             | (Extract Spatial Matrices)
             v
 [ Wrist Anchor Node Update ]
             |
             | (Re-center Overlay Transforms)
             v
 [ Application Render Output ]

```

### 3.2 Injection Interaction Sequence

The state machine advances based on physical proximity and contact detection between simulated items.

```
+------------------+         +-----------------------+         +---------------------+
|  State: Idle     | ------> | State: Contact Found  | ------> | State: Penetration  |
|                  |         |                       |         |                     |
| Needle searching |         | Raycast meets mesh    |         | Depth tracking active|
| for hand surface |         | Capture initial angle |         | Calculate metrics   |
+------------------+         +-----------------------+         +---------------------+
                                                                          |
                                                                          v
                                                               +---------------------+
                                                               | State: Evaluation   |
                                                               |                     |
                                                               | Freeze coordinates  |
                                                               | Generate score data |
                                                               +---------------------+

```

4\. Technical Technical Specifications & Pipeline Configurations
----------------------------------------------------------------

### 4.1 Graphics Pipeline Setup

The rendering configuration forces hardware optimizations suited for standalone mobile GPUs.

| **Feature Pipeline** | **Configuration Selection** |
| --- | --- |
| **Render Architecture** | Single-Pass Instanced (Reduces CPU draw calls) |
| **Color Space** | Linear (Maintains accurate lighting gradients) |
| **Lighting Mode** | Forward Rendering with URP Lit Shaders |
| **Texture Compressing** | ASTC Format Block Footprint |

### 4.2 Asset Optimization Matrix

To respect hardware memory and rendering limits, incoming assets must match strict mesh and material limits.

```
Total Rendering Scene Polygon Budget: Max 30,000 Triangles
├─ Hand Mesh Layer: 12,000 Triangles Max (2 LOD Steps allocated)
├─ Interacted Tool Asset: 4,000 Triangles Max
└─ Static Environment Room: 6,000 Triangles Max

```

-   **Material Limitations:** Assets share packed texture sets (Albedo, Roughness, Normal maps) to keep total graphics memory usage below 64 MB.

5\. System Integration & Execution Logic
----------------------------------------

The calculation of insertion success requires explicit geometric vector logic executed at the moment of object intersection.

### 5.1 Needle Angle Evaluation Formula

The angle indicator calculates the angular variance between the active vector direction of the syringe tool and the normal vector of the collision surface point.

$$\theta = \arccos\left(\frac{\mathbf{V}_{\text{needle}} \cdot \mathbf{N}_{\text{skin}}}{\|\mathbf{V}_{\text{needle}}\| \|\mathbf{N}_{\text{skin}}\|}\right)$$

```
       V_needle (Syringe Direction Vector)

           \   theta (Computed Entry Angle)
____________\_________________
    \       /
     \     /
      \   /  N_skin (Surface Normal Vector)
       \ /
  ============== [ Hand Mesh Surface ] ==============

```

### 5.2 Dynamic Positioning Anchor Logic

To prevent visual floating or drifting of overlays during physical movement, child rendering components calculate their orientation relative to a persistent tracked root node.

$$\mathbf{M}_{\text{overlay}} = \mathbf{M}_{\text{wrist\_anchor}} \times \mathbf{M}_{\text{local\_offset}}$$

This matrix transformation ensures that regardless of tracking system adjustments, the anatomical visualization maps stay consistently aligned with the physical user.

6\. Failure Modes, Security & Mitigation Strategies
---------------------------------------------------

### 6.1 Tracking Instability & Loss Management

-   **Symptom:** Optical cameras lose clear sight of hand joints due to ambient lighting shifts.

-   **Mitigation Logic:** The system implements a state validation check. If tracking data confidence flags drop below an acceptable threshold, the app triggers a session pause state, retains current scoring metrics in volatile memory, and displays a recovery prompt to the user.

### 6.2 Rendering Overload & Thermal Management

-   **Symptom:** Performance drops below the necessary 60 frames per second due to complex mesh operations or excessive shader passes.

-   **Mitigation Logic:** The engine activates Level of Detail (LOD) mesh simplification steps. It replaces multi-layered skin shaders with simplified single-pass vertex operations if frame timing thresholds are breached.

### 6.3 Input Validation Failures

-   **Symptom:** Raycast operations pass entirely through colliders due to frame-rate drops or high-velocity movements.

-   **Mitigation Logic:** Raycast execution runs at a constant rate within fixed physics update steps rather than variable frame updates. It uses continuous collision sweep logic to catch intersections between frame intervals.

7\. Deployment, Validation & Verification Architecture
------------------------------------------------------

```
+--------------------------+      +---------------------------+      +------------------------+
|  Build Automation Stage  | ---> | Verification Stage        | ---> | Target Deployment      |
|                          |      |                           |      |                        |
| Compile Android Binary   |      | Static Size Checks        |      | Standalone Sideloading |
| Strip Debug Symbols      |      | Dependency Tree Profiling |      | Run-time Optimization  |
+--------------------------+      +---------------------------+      +------------------------+

```

### 7.1 Automated Assembly Validation

The build output pipeline passes through three strict automated verification gates before deployment to testing devices:

1.  **Package Analysis:** Automated inspection verifies that the resulting standalone installation file doesn't breach the structural 500 MB limit.

2.  **Asset Enforcement Check:** Asset processing blocks compilation if uncompressed texture sheets or non-optimized meshes are discovered in the final production package.

3.  **API Verification Check:** The system verifies that explicit target SDK flags match specified firmware versions, ensuring full compatibility with the deployment platform.
