**TASK LIST**

**AR/VR Injection Precision Trainer**

Development task breakdown across Unity (engine) and Blender (asset pipeline). Tasks are listed in recommended execution order.

| **6**<br><br>Unity tasks | **7**<br><br>Blender tasks | **13**<br><br>Total tasks |
| ------------------------ | -------------------------- | ------------------------- |

**Unity - Engine & Application**

| **#**  | **Task**                    | **Notes**                                                                                                                 | **Status**    |
| ------ | --------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ------------- |
| **01** | **New project setup**       | Create Unity 2022.3 LTS project with URP (Mobile) template; configure XR Plugin Management for Pico 4                     | _Not started_ |
| **02** | **Verify hand tracking**    | Integrate PICO Unity SDK v3.x; confirm optical hand tracking input on device; set up XR Rig                               | _Not started_ |
| **03** | **Create PBR texture set**  | Author base colour, roughness, metallic, and normal maps; compress to ASTC; keep total texture memory < 64 MB             | _Not started_ |
| **04** | **Make basic hierarchy**    | Build scene hierarchy: XR Rig > Camera, Wrist Anchor, Hand Mesh, Syringe, UI Canvas (world space)                         | _Not started_ |
| **05** | **Injection mechanics**     | Implement needle entry detection (MeshCollider + Raycast), angle computation, depth tracking, and composite scoring logic | _Not started_ |
| **06** | **UI/UX and optimisations** | Build all five screens in XR UI Toolkit; profile for >= 60 fps; APK size < 500 MB; hand tracking latency < 15 ms          | _Not started_ |

**Blender - Asset Pipeline**

| **#**  | **Task**               | **Notes**                                                                                                                      | **Status**    |
| ------ | ---------------------- | ------------------------------------------------------------------------------------------------------------------------------ | ------------- |
| **01** | **Create bones**       | Build armature for hand/wrist; ensure joint placement aligns with Pico hand tracking skeleton for clean deformation            | _Not started_ |
| **02** | **Create muscles**     | Model underlying muscle volumes to give the hand anatomically plausible form under the skin layer                              | _Not started_ |
| **03** | **Design veins**       | Sculpt or place dorsal hand veins following anatomical atlas reference; produce vein map texture for AR overlay                | _Not started_ |
| **04** | **Add skin layer**     | Model and weight-paint skin mesh; author PBR skin shader (base colour, subsurface scattering hint, roughness, normal map)      | _Not started_ |
| **05** | **Optimise for Unity** | Reduce hand mesh to 12,000 tris; environment to 6,000 tris; UV-pack all meshes; export as FBX; verify materials import cleanly | _Not started_ |
| **06** | **Syringe assembly**   | Model barrel, plunger, needle hub, and needle; keep total under 4,000 tris; apply 1024 x 1024 px PBR texture set               | _Not started_ |
| **07** | **UV unwrap**          | Unwrap all meshes with minimal stretching; pack UVs efficiently; bake AO and normal maps ready for Unity import                | _Not started_ |

**Status key**

| _Not started_ | _In progress_ | _Blocked_ | _Done_ |
| ------------- | ------------- | --------- | ------ |
