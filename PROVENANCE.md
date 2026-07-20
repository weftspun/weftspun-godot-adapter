# Provenance

Source: `v-sekai-multiplayer-fabric/fabric-godot-core`, tag
`v2026.06.27.1907-multiplayer-fabric` (commit `2cecde7`,
"Merge branch 'multiplayer-fabric'"), itself a fork of
`godotengine/godot`. MIT-licensed.

This is a keep-or-delete extraction only — every kept file is
byte-identical to the source tag. No code has been modified, ported,
or rewritten yet. That is deliberately separate future work.

## Kept (whole directories, unmodified)

- `core/` — Object/Variant/ClassDB, always required
- `servers/` — RenderingServer, DisplayServer, the `RendererCompositor`
  extension point a future custom renderer would implement
- `scene/` — Node/SceneTree/3D nodes, for real gameplay
- `main/` — engine entry point
- root build files: `SConstruct`, `methods.py`, `platform_methods.py`,
  `gles3_builders.py`, `glsl_builders.py`, `scu_builders.py`,
  `version.py`
- repo hygiene: `.clang-format`, `.clang-tidy`, `.clangd`,
  `.editorconfig`, `.gitattributes`, `.gitignore`, `README.md`,
  `LICENSE.txt`, `COPYRIGHT.txt`, `AUTHORS.md`

## `platform/` — kept only

- `linuxbsd/`, `windows/`

Deleted: `android/`, `ios/`, `macos/`, `visionos/`, `web/`.

## `drivers/` — kept only

- `vulkan` (GPU rendering, cross-platform across the two kept
  platforms)
- `unix`, `windows` (platform glue for the two kept platforms)
- `backtrace`, `png`
- `register_driver_types.{cpp,h}`, `SCsub`

Deleted: `accesskit`, `alsa`, `alsamidi`, `apple`, `apple_embedded`,
`coreaudio`, `coremidi`, `d3d12`, `egl`, `gl_context`, `gles3`,
`metal`, `pulseaudio`, `sdl`, `wasapi`, `winmidi`, `xaudio2` — no
audio, no non-Vulkan GPU backend, no Apple platform code, no
accessibility/input-library layer yet. Add back if/when actually
needed; not yet justified by a real requirement.

## `modules/` — kept only

- `freetype`, `regex`, `glslang`, `text_server_adv`, `svg`
- scaffolding: `modules_builders.py`, `register_module_types.h`,
  `SCsub`

Deleted (all the rest): `astcenc`, `basis_universal`, `bcdec`,
`betsy`, `bmp`, `camera`, `cassie`, `csg`, `cvtt`, `dds`, `enet`,
`etcpak`, `fbx`, `gdscript`, `gltf`, `godot_physics_2d`,
`godot_physics_3d`, `gridmap`, `hdr`, `http3`, `interactive_music`,
`jolt_physics`, `jpg`, `jsonrpc`, `keychain`, `ktx`, `lasso`,
`lightmapper_rd`, `mbedtls`, `meshoptimizer`, `mobile_vr`, `mono`,
`mp3`, `msdfgen`, `multiplayer`, `multiplayer_fabric`,
`multiplayer_fabric_asset`, `native_media`, `navigation_2d`,
`navigation_3d`, `noise`, `objectdb_profiler`, `ogg`, `open_telemetry`,
`openxr`, `raycast`, `sandbox`, `speech`, `sqlite`, `text_server_fb`,
`tga`, `theora`, `tinyexr`, `upnp`, `vhacd`, `visual_shader`, `vorbis`,
`webp`, `webrtc`, `websocket`, `webxr`, `xatlas_unwrap`, `xr_grid`,
`zip`.

Reasoning: no physics modules (`godot_physics_2d/3d`, `jolt_physics`)
— simulation is authoritative server-side in `weft-warp-loop`'s
`fanout-core`, not client-side Godot physics (ADR 0051). No Godot-own
networking (`enet`, `websocket`, `webrtc`, `multiplayer`, `upnp`) —
this adapter's own connection reuses `fanout_load_client`'s existing
QUIC/ZPB code directly, not Godot's networking stack. No `mono`
(C#) — not used. No `gdscript` — `weft-warp-loop` already has its own
scripting system (the s7 Lisp-1 sandboxed in libriscv, ADR 0006/0011/
0028); this adapter has no reason to run a second, separate scripting
language on top of it. No VR/XR modules (`openxr`, `webxr`,
`mobile_vr`, `xr_grid`) — not in scope for this pass. This org's own
additions (`cassie`, `http3`, `sandbox`, `multiplayer_fabric*`,
`lasso`, `keychain`, `native_media`, `open_telemetry`, `speech`,
`sqlite`) are excluded for now too — real candidates for later, not
yet justified here.

## `misc/dist/` — kept only

- `document_icons`, `linux`, `shell`, `windows`

Deleted: `apple_embedded_xcode`, `html`, `macos`, `macos_template.app`,
`macos_tools.app`.

## Not yet done

- No build attempt yet — this set is reasoned, not build-verified.
- No code changes (custom `RendererCompositor` implementation, module
  re-additions if a build reveals something's actually needed, mass
  reduction beyond directory-level pruning) — next steps, not this
  pass.
