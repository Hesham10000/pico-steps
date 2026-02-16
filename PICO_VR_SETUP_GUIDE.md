# PICO VR App Setup Guide: Window + Button

**Target Stack:** Unity 6.3 LTS (6000.3) | XR Interaction Toolkit 3.3.1 | PICO Unity Integration SDK 3.3.0 | PICO Building Blocks

This guide walks you through creating a simple PICO VR app with a floating window and a button, using PICO Building Blocks so you get all PICO features (init scene, seethrough, controllers, etc.) out of the box — no reinventing the wheel.

---

## Prerequisites

| What | Where to get it |
|------|----------------|
| Unity Hub | [unity.com/download](https://unity.com/download) |
| Unity 6.3 LTS | Install through Unity Hub (version 6000.3.x) |
| PICO headset (PICO 4 / PICO 4 Ultra) | System version 5.13.0+ for PICO 4, 5.14.0+ for PICO 4 Ultra |
| PICO Developer account | [developer.picoxr.com](https://developer.picoxr.com/) |
| USB-C cable | For deploying to headset |

---

## Part 1: Install Unity 6.3 LTS

1. **Download and install Unity Hub** from [unity.com/download](https://unity.com/download)
2. Open Unity Hub, go to the **Installs** tab on the left sidebar
3. Click **Install Editor**
4. Find **Unity 6.3 LTS (6000.3.x)** and click **Install**
5. **IMPORTANT** — On the "Add modules" screen, check these boxes:
   - **Android Build Support**
   - **Android SDK & NDK Tools** (nested under Android Build Support)
   - **OpenJDK** (nested under Android Build Support)
6. Click **Install** and wait for it to finish (this can take 15-30 minutes)

---

## Part 2: Create a New Unity Project

1. In Unity Hub, click the **New Project** button (top right)
2. Select **Unity 6.3 LTS** as the editor version (dropdown at the top)
3. Choose the **3D (URP)** template — URP (Universal Render Pipeline) is recommended for PICO
4. Give your project a name (e.g., `MyPicoApp`)
5. Choose a location on your computer
6. Click **Create project**
7. Wait for Unity to create and open the project (first time takes a few minutes)

---

## Part 3: Install the PICO Unity Integration SDK

1. In Unity, go to **Window > Package Manager**
2. Click the **+** button in the top-left corner of the Package Manager window
3. Select **Add package from git URL...**
4. Paste this URL and click **Add**:
   ```
   https://github.com/Pico-Developer/PICO-Unity-Integration-SDK.git
   ```
5. Wait for the SDK to download and import (this may take a few minutes)
6. If a dialog pops up asking you to enable the new Input System, click **Yes** and let Unity restart
7. If prompted about XR Plugin Management, click **Yes** to accept

> **Note:** The PICO Unity Integration SDK 3.3.0 supports Unity 6, XR Interaction Toolkit 3.x, and AR Foundation 6.0.

---

## Part 4: Install XR Interaction Toolkit 3.3.1

1. Go to **Window > Package Manager**
2. In the top-left dropdown that says "Packages:", change it to **Unity Registry**
3. Search for **XR Interaction Toolkit**
4. Select it and check that version **3.3.1** is available (use the version dropdown if needed)
5. Click **Install**
6. After installation, a popup may appear asking to import **Starter Assets** — click **Import**. These are very useful prefabs for controllers, hand tracking, and interaction
7. Also import the **XR Device Simulator** sample if offered — this lets you test in the editor without a headset

---

## Part 5: Configure XR Plugin Management for PICO

1. Go to **Edit > Project Settings**
2. In the left sidebar, click **XR Plug-in Management**
3. Click the **Android tab** (the little Android robot icon)
4. Check the box for **PICO** to enable the PICO XR plugin
5. Still in Project Settings, expand **XR Plug-in Management** and click **PICO** (sub-item)
6. Here you can configure PICO-specific settings — leave defaults for now

---

## Part 6: Configure Android Build Settings

1. Go to **File > Build Settings** (or **File > Build Profiles** in Unity 6.3)
2. Select **Android** from the platform list
3. Click **Switch Platform** (if not already on Android)
4. Click **Player Settings** (bottom left of Build Settings window)
5. In Player Settings, configure these **critical settings**:
   - **Other Settings > Rendering:**
     - Set **Color Space** to **Linear**
     - Uncheck **Auto Graphics API**, then remove **Vulkan** and keep only **OpenGLES3** (or keep Vulkan if your PICO device supports it)
   - **Other Settings > Identification:**
     - Set **Minimum API Level** to **Android 10.0 (API level 29)** or higher
     - Set **Target API Level** to **Automatic (highest installed)**
   - **Other Settings > Configuration:**
     - Set **Scripting Backend** to **IL2CPP** (required by PICO SDK)
     - Set **Target Architectures** to **ARM64 only** (uncheck ARMv7, required by PICO SDK)
   - **Other Settings > Identification:**
     - Set a unique **Package Name** (e.g., `com.yourname.mypicoapp`)

---

## Part 7: Use PICO Building Blocks to Set Up the Scene

This is the key step where you avoid reinventing the wheel. PICO Building Blocks configure everything for you with one click.

1. Go to **Window > PICO Building Blocks** to open the Building Blocks panel
2. You will see a list of available building blocks. Click/add the following blocks:

### Required Blocks:

- **Init Scene** — This is the most important block. It sets up:
  - XR Origin (camera rig) correctly configured for PICO
  - Controller tracking
  - Hand tracking support
  - Input system bindings
  - Event system for UI interaction
  - All the basic XR infrastructure you need

  **Click the "Add" or "+" button next to Init Scene.** This will automatically configure your current scene with all the necessary PICO XR components.

- **Video Seethrough** (Passthrough) — Enables the camera passthrough so users can see the real world through the headset cameras:
  - **Click "Add" next to Video Seethrough**
  - This configures the camera background to show passthrough instead of a skybox
  - The camera background will become transparent, showing the real world
  - Once enabled, it works throughout the lifecycle of the app

### Optional Blocks (add as needed):

| Block | What it does |
|-------|-------------|
| **PICO Spatial Mesh** | Scans the room and creates a 3D mesh of your environment |
| **PICO Scene Capture** | Captures the user's room layout for MR apps |
| **PICO Composition Layer Overlay** | Renders high-quality overlays on top of the scene |
| **PICO Composition Layer Underlay** | Renders layers behind the scene |
| **Body Tracking** | Full body tracking support |
| **Object Tracking** | Track real-world objects |
| **Spatial Audio** | 3D spatial audio support |

3. After adding the blocks, your scene Hierarchy should now contain:
   - **XR Origin** (with Main Camera, Left/Right Controllers)
   - **XR Interaction Manager**
   - **Event System** (with XR UI Input Module)
   - Any seethrough/passthrough components on the camera

> **Important URP Note:** If you have URP enabled and Video Seethrough active, make sure **Post Processing is disabled** on the Main Camera. Having both enabled at the same time will cause an error.

---

## Part 8: Create the Window (World Space Canvas)

In VR, a "window" is a **World Space Canvas** — a flat panel that floats in 3D space.

1. In the Hierarchy panel (left side), right-click on empty space
2. Go to **GameObject > XR > UI Canvas** (if available) OR **GameObject > UI > Canvas**
3. Select the new **Canvas** in the Hierarchy
4. In the **Inspector** panel (right side), find the **Canvas** component:
   - Change **Render Mode** to **World Space**
5. Set up the Canvas transform (in the **Rect Transform** component at the top of the Inspector):
   - **Position:** X = `0`, Y = `1.3`, Z = `2` (this places it 2 meters in front, roughly at eye level)
   - **Width:** `800`, **Height:** `600`
   - **Scale:** X = `0.001`, Y = `0.001`, Z = `0.001` (this is crucial — World Space canvases need to be scaled way down to look normal-sized in VR)
6. The Canvas should already have a **Graphic Raycaster** component. If it's the standard one, **replace it**:
   - Remove the existing **Graphic Raycaster** component (click the three dots on the component header > Remove Component)
   - Click **Add Component**, search for and add **Tracked Device Graphic Raycaster** — this allows VR controllers/hands to interact with the UI

### Add a Background Panel:

1. Right-click on the **Canvas** in the Hierarchy
2. Select **UI > Panel**
3. This creates a background panel — you can change its color in the **Image** component in the Inspector
4. For a clean look, set the Image color to something semi-transparent (e.g., dark gray with alpha ~200)

---

## Part 9: Add a Button to the Window

1. Right-click on the **Panel** in the Hierarchy
2. Select **UI > Button - TextMeshPro**
3. If prompted to import TMP Essentials, click **Import TMP Essentials**
4. Select the new **Button** in the Hierarchy
5. Configure the button:
   - In the **Rect Transform**, adjust the size:
     - **Width:** `300`, **Height:** `80`
     - **Position:** X = `0`, Y = `0`, Z = `0` (centered on the panel)
6. Expand the Button in the Hierarchy to find the **Text (TMP)** child object
7. Select the **Text (TMP)** object
8. In the Inspector, find the **TextMeshPro - Text (UI)** component
9. Change the **Text** field to whatever you want (e.g., `Click Me!`)
10. Adjust the **Font Size** to something readable (e.g., `36`)

### Make the Button Do Something:

1. Select the **Button** object in the Hierarchy
2. In the Inspector, scroll down to the **On Click ()** section
3. Click the **+** button to add an event
4. For a quick test, you can have it log a message:
   - Drag any GameObject into the object field
   - Or leave it for now — you'll wire it up when you add actual functionality later

---

## Part 10: Verify the Event System

The PICO Building Blocks "Init Scene" should have already set this up, but verify:

1. Check the **Hierarchy** for an **EventSystem** GameObject
2. Select it and verify it has:
   - **Event System** component
   - **XR UI Input Module** component (NOT the old Standalone Input Module)
3. If the XR UI Input Module is missing, add it:
   - Select the EventSystem
   - Click **Add Component**
   - Search for **XR UI Input Module** and add it
   - Remove the old **Standalone Input Module** if present

---

## Part 11: Test in the Editor (Optional)

Before deploying to the headset, you can test in the Unity editor:

1. If you imported the **XR Device Simulator** sample earlier, it should work automatically
2. Press the **Play** button at the top of the Unity editor
3. You should see your canvas floating in 3D space
4. Use the XR Device Simulator controls (WASD to move, mouse to look, left/right click for controllers) to point at the button and click it

---

## Part 12: Build and Deploy to PICO Headset

### Prepare Your PICO Headset:

1. On your PICO headset, go to **Settings > General > Developer Mode**
2. Turn on **Developer Mode** (you may need to register as a developer at [developer.picoxr.com](https://developer.picoxr.com/) first)
3. Turn on **USB Debugging**
4. Connect your PICO headset to your computer with a USB-C cable
5. Put on the headset — you should see a popup asking to allow USB debugging. Click **Allow**

### Build from Unity:

1. Go to **File > Build Settings** (or **File > Build Profiles**)
2. Make sure **Android** is the selected platform
3. Click **Refresh** next to the Run Device dropdown — your PICO headset should appear
4. Select your PICO headset from the dropdown
5. Click **Build and Run**
6. Choose a location to save the APK file
7. Wait for the build to complete (first build takes several minutes due to IL2CPP compilation)
8. The app will automatically install and launch on your headset

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| PICO not showing in XR Plug-in Management | Make sure PICO SDK is properly installed via Package Manager |
| Build fails with "ARM64 required" | Go to Player Settings > Other Settings > Target Architectures > Check ARM64, uncheck ARMv7 |
| Build fails with "IL2CPP required" | Go to Player Settings > Other Settings > Scripting Backend > Select IL2CPP |
| Seethrough not working | Make sure Post Processing is OFF on Main Camera when using URP + Video Seethrough |
| Button not clickable in VR | Make sure Canvas has **Tracked Device Graphic Raycaster** (not regular Graphic Raycaster) and EventSystem has **XR UI Input Module** |
| Canvas too big / too small | Adjust the Scale on the Canvas Rect Transform. `0.001` on all axes is a good starting point |
| Black screen on headset | Check that the PICO XR plugin is enabled in XR Plug-in Management under the Android tab |
| Controllers not visible | The Init Scene building block should handle this; if not, make sure controller prefabs from XRI Starter Assets are assigned in the XR Origin |

---

## Project Hierarchy Overview

After completing all steps, your scene Hierarchy should look roughly like this:

```
Scene
├── XR Origin                          (from PICO Building Blocks - Init Scene)
│   ├── Camera Offset
│   │   ├── Main Camera                (with seethrough configured)
│   │   ├── Left Controller
│   │   └── Right Controller
├── XR Interaction Manager             (from Init Scene)
├── EventSystem                        (from Init Scene, with XR UI Input Module)
├── Canvas (World Space)               (your window)
│   └── Panel                          (background)
│       └── Button                     (your button)
│           └── Text (TMP)             (button label)
└── Directional Light
```

---

## Summary of What Each Tool Does

| Tool | Purpose |
|------|---------|
| **Unity 6.3 LTS** | The game engine / editor where you build everything |
| **XR Interaction Toolkit 3.3.1** | Unity's framework for VR interactions (grabbing, pointing, UI interaction, locomotion) |
| **PICO Unity Integration SDK 3.3.0** | PICO-specific plugin that makes Unity work with PICO headsets |
| **PICO Building Blocks** | One-click setup tool within the SDK that configures complex XR features automatically |
| **URP (Universal Render Pipeline)** | Unity's modern rendering pipeline, recommended for PICO |

---

## Useful Links

- [PICO Developer Documentation](https://developer.picoxr.com/document/unity/)
- [PICO Building Blocks Documentation](https://developer.picoxr.com/document/unity/pico-building-blocks/)
- [PICO Video Seethrough Documentation](https://developer.picoxr.com/document/unity/seethrough/)
- [PICO Unity Integration SDK (GitHub)](https://github.com/Pico-Developer/PICO-Unity-Integration-SDK)
- [PICO Interaction Sample (GitHub)](https://github.com/Pico-Developer/InteractionSample-Unity)
- [PICO MR Sample (GitHub)](https://github.com/Pico-Developer/MRSample-Unity)
- [XR Interaction Toolkit 3.3.1 Documentation](https://docs.unity3d.com/Packages/com.unity.xr.interaction.toolkit@3.3/changelog/CHANGELOG.html)
- [Unity XRI UI Canvas Setup Guide](https://docs.unity3d.com/Packages/com.unity.xr.interaction.toolkit@3.0/manual/ui-setup.html)
- [Unity Best Practices for VR UI](https://learn.unity.com/tutorial/best-practices-for-user-interfaces-ui-in-vr-with-the-xr-interaction-toolkit)
- [Unity 6.3 LTS Release Blog](https://unity.com/blog/unity-6-3-lts-is-now-available)
