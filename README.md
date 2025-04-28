<p align="center">
  <img src="https://via.placeholder.com/150/0000FF/808080?text=URP+Unlocker" alt="URP Unlocker Logo">
</p>

[![Version](https://img.shields.io/badge/version-1.0.0-brightgreen)](https://github.com/your-username/your-repo-name)
[![Tested with Unity](https://img.shields.io/badge/Unity-6.0%2B-blueviolet)](https://unity3d.com/get-unity/download) <!-- Update Unity version if needed -->

# URP Unlocker

URP Unlocker provides a straightforward way to modify Universal Render Pipeline (URP) Asset settings directly from your C# scripts at runtime.
Gain control over rendering, quality, lighting, shadows, and post-processing parameters, including those normally hidden or locked in the editor with just a single line of code per setting.
It also includes helpers for easily saving and loading URP configurations.

**Key Features:**

*   **Unlock Hidden Parameters:** Access and modify any locked URP setting with just one line of code
*   **Concise API:** Modify URP Asset using a clear, chainable property structure (e.g., `API.CurrentUnlockedURPAsset.Lighting.MainLightMode = ...`).
*   **Targeted Control:** Modify the globally active URP Asset or specific instances you provide.
*   **Save/Load URP Settings:** Serialize and deserialize URP configs to binary/json data for saving and loading presets.

## Table of Contents

- [URP Unlocker](#urp-unlocker)
  - [Key Features](#key-features)
  - [Table of Contents](#table-of-contents)
  - [How to Use](#how-to-use)
    - [Including Namespaces](#including-namespaces)
    - [Modifying the Active URP Asset](#modifying-the-active-urp-asset)
    - [Modifying a Specific URP Asset](#modifying-a-specific-urp-asset)
    - [Serialization/Deserialization of URP Asset(Binary Serialization)](#serializationdeserialization-of-the-urp-asset-binaryjson)
    - [Save/Load URP Asset](#saveload-urp-asset)

## How to Use

First, ensure you have the necessary namespaces included in your script:

```csharp
using URPUnlocker.API;             // Core API access
using URPUnlocker.Core;            // For UnlockedURPAsset and SavableURPA
using UnityEngine;               // For types like Vector2
using UnityEngine.Rendering;
using UnityEngine.Rendering.Universal; // For URP specific enums (LightRenderingMode, ShadowResolution, etc.)
using ShadowResolution = UnityEngine.Rendering.Universal.ShadowResolution; // For shadows settings
```

<br>

### Modifying the Active URP Asset

To change settings on the URP Asset currently active in `GraphicsSettings`:

```csharp
// Access the wrapper for the currently active URP Asset
UnlockedURPAsset currentURPA = URPUnlockerAPI.CurrentUnlockedURPAsset;

if (currentURPA != null)
{
    // Example: Change lighting mode and enable main light shadows
    currentURPA.Lighting.MainLightMode = LightRenderingMode.PerPixel;
    currentURPA.Lighting.MainLightShadowsCasting = true;

    // Example: Increase shadow resolution and adjust shadow distance
    // Note: Direct access to properties, not methods like the example provided. Adjusting based on previous code analysis.
    currentURPA.Lighting.AdditionalLightShadowResolution = ShadowResolution._1024; // Assuming this exists for additional lights
    currentURPA.Shadows.ShadowDistance = 120f;

    // Example: Enable HDR rendering
    currentURPA.Quality.HDR = true;
}
else
{
    Debug.LogError("No active URP Asset found!");
}
```

<br>

### Modifying a Specific URP Asset

If you have a reference to a specific `UniversalRenderPipelineAsset` instance, you can wrap it and modify it directly:

```csharp
// Assume 'myUrpAsset' is a variable holding your specific UniversalRenderPipelineAsset
UniversalRenderPipelineAsset myUrpAsset = YOUR_URPA; // Your method to get the asset

if (myUrpAsset != null)
{
    // Create an unlocked wrapper for this specific asset
    UnlockedURPAsset specificURPA = new UnlockedURPAsset(myUrpAsset);

    // Now modify settings on this specific instance
    specificURPA.Rendering.SRPBatcher = true;
    specificURPA.Quality.RenderScale = 1.1f; // Set render scale to 110%
    specificURPA.Shadows.SoftShadows = true;
    specificURPA.PostProcessing.ColorGradingMode = ColorGradingMode.HighDynamicRange;
    specificURPA.Shadows.CascadeCount = 4; // Use 4 shadow cascades
}
```

<br>

### Serialization/Deserialization of the URP Asset (Binary/JSON)

Serialize/deserialize URP asset in one line of code.

#### JSON

```c#
var jsonSerializedURP = URPUnlockerAPI.SerializeURPA(SavableURPA urpa);
var deserializedURP = URPUnlockerAPI.DeserializeURPA(serializedURP);
```

---

#### Binary

```c#
var binarySerializedURP = URPUnlockerAPI.SerializeURPAToBinary(SavableURPA urpa);
var deserializedURP = URPUnlockerAPI.DeserializeURPAFromBinary(binarySerializedURP);
```

---

### Save/Load URP Asset

#### JSON saving

```csharp
URPUnlockerAPI.SaveURPA(URPAsset, "key"); // SAVE
SavableURPA loadedSavableURPA = URPUnlockerAPI.GetSavedURPA("key"); // LOAD
URPUnlockerAPI.ApplyConfiguration(loadedSavableURPA); // Apply loaded URP save
```

#### Binary saving

```csharp
URPUnlockerAPI.SaveURPAToBinaryFile("key", urpa) // SAVE
SavableURPA loadedSavableURPA = URPUnlockerAPI.LoadURPAFromBinaryFile("key"); // LOAD
URPUnlockerAPI.ApplyConfiguration(loadedSavableURPA); // Apply loaded URP save
```
