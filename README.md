# Unity TinyEXR

[syoyo/tinyexr](https://github.com/syoyo/tinyexr) wrapper for Unity.

It loads EXR bytes or files directly in Unity and returns `UnityEngine.Color[]`.

## Build the native plugin

```powershell
cmake -S native -B native/build -A x64
cmake --build native/build --config Release
```

The Windows x64 plugin is emitted to:

```text
TinyExr/x64/TinyExr.dll
```

Copy the `TinyExr` folder into your Unity project's `Assets` folder.

## Project layout

```text
TinyExr/                         Unity runtime files
  TinyExr.cs
  x64/TinyExr.dll

native/                          Native plugin source and build files
  src/                            Unity-facing wrapper exports
  tinyexr/                        syoyo/tinyexr files in upstream layout
```

## Unity usage

```csharp
using UnityEngine;

public class ExrExample : MonoBehaviour
{
    public string path;

    void Start()
    {
        TinyExr.ImageResult image = TinyExr.LoadFile(path);

        int width = image.width;
        int height = image.height;
        Color[] colors = image.colors;

        Texture2D texture = image.ToTexture2D(linear: true);
    }
}
```

For only `Color[]`:

```csharp
Color[] colors = TinyExr.LoadColors(bytes, out int width, out int height);
```

`TinyExr.FlipVerticallyOnLoad` defaults to `true` to match Unity texture coordinates. Pass `false` to `Load(...)` or `LoadFile(...)` if you want the raw row order returned by tinyexr.

## Notes

- Uses tinyexr's stable v1 `LoadEXRFromMemory` API and bundled `miniz`.
- Returns RGBA float data, mapped one-to-one into `UnityEngine.Color`.
- Layered EXR channels such as `diffuse.R` require tinyexr's layer-specific API and are not exposed by this minimal wrapper yet.
