# 3MF Viewer

A small, fast native Mac app for `.3mf` 3D-print files. It works like Preview does for STL.

- **Finder thumbnails** show the object itself in its filament colours, instead of a blank icon
- **Quick Look:** select a file and press **Space** to rotate and zoom a 3D preview
- **Viewer window** (double-click a file): rotate, zoom, 10 mm build-plate grid, and an info bar with
  size in mm (X × Y × Z), triangle count, number of colours and file size

Reads project files from **Bambu Studio, OrcaSlicer, PrusaSlicer and Cura**, including per-object and
per-part filament colours, painted (multi-colour) areas and multi-file assemblies. Sliced files with no
mesh show the slicer's embedded preview image.

Swift + SceneKit, no third-party dependencies. Needs macOS 12 Monterey or later. Builds a universal app
(Apple Silicon + Intel).

## Build and install

You need Apple's Command Line Tools (`xcode-select --install`). Full Xcode isn't required.

```bash
git clone https://github.com/rahul576/3mf-viewer.git
cd 3mf-viewer
bash build.sh
```

The script:

1. compiles the app plus two Quick Look extensions (Finder thumbnails and the Space-bar preview)
2. signs them locally (ad-hoc) and installs **3MF Viewer** in `/Applications`
3. turns on the extensions and makes the app the default for `.3mf` files
4. saves a ready-to-share **`~/Downloads/3MF Viewer.zip`** (the app and `How to install.txt`)
5. restarts Finder so the new thumbnails show up

## Sharing with someone who doesn't build it

Send them `~/Downloads/3MF Viewer.zip`. They:

1. drag **3MF Viewer.app** into Applications
2. right-click it › **Open** the first time (if macOS still blocks it: System Settings › Privacy &
   Security › **Open Anyway**). This is needed because the app isn't notarised by Apple.
3. click **Use 3MF Viewer** when it asks to open `.3mf` files

Thumbnails appear after the app has been opened once.

## Controls

| Action | How |
|---|---|
| Rotate | drag |
| Zoom | scroll wheel, trackpad pinch, `+` / `−`, or View › Zoom In / Zoom Out |
| Reset view | `R` or `⌘0` |
| Show/hide grid | `⌘G` |
| Open more files | `⌘O`, or drag files onto the window |

## Project layout

```
Sources/
  ZipArchive.swift      read-only ZIP reader (stored + deflate, ZIP64), works inside the sandbox
  ThreeMF.swift         3MF parser: meshes, components, transforms, filament & paint colours
  SceneBuilder.swift    SceneKit scene, lighting, grid, zoomable view, off-screen thumbnail render
  main.swift            the viewer app (windows, menus, default-app handling)
Extensions/
  ThumbnailProvider.swift       Finder thumbnail extension
  PreviewViewController.swift   Space-bar Quick Look preview extension
  *-Info.plist, Extension.entitlements
Resources/icon_1024.png  app icon source
Info.plist               app bundle info, declares the 3MF file type
build.sh                 build, sign, install, package
```

## Troubleshooting

- **Thumbnails don't show:** open the app once, then run `qlmanage -r cache` and reopen the Finder window.
  Check that the extensions are on: `pluginkit -m -v | grep threemfviewer`.
- **Not opening on double-click:** in the app, choose 3MF Viewer › Make Default for .3mf Files. Or in Finder,
  select a `.3mf` › File › Get Info › Open with › 3MF Viewer › **Change All**.
- **Going back to Bambu Studio:** do the same Get Info steps and pick Bambu Studio.

## License

MIT. See [LICENSE](LICENSE).
