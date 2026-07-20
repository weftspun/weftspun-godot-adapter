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

- `freetype`, `regex`, `glslang`, `text_server_adv`, `svg`, `sandbox`,
  `jolt_physics`, `sqlite`, `open_telemetry`, `astcenc`,
  `basis_universal`, `bcdec`, `betsy`, `cvtt`, `etcpak`
- scaffolding: `modules_builders.py`, `register_module_types.h`,
  `SCsub`

`sandbox` is `libriscv/godot-sandbox` — the same libriscv substrate
`weft-warp-loop` already vendors (`flow-toolchain/thirdparty/libriscv`)
and already runs the s7 Lisp-1 scripting tier in, just wearing Godot's
own GDExtension/Variant-ABI/resource-loading clothes. Initially
deleted on the assumption that not using GDScript meant not needing
anything sandbox-adjacent either; that was wrong — this is the
closest thing to what this project already runs, and could let the
adapter host the already-built, already-verified `s7_guest.elf`
through Godot's own proven loading mechanism instead of writing new
FFI/embedding glue from scratch. Its own vendored
`thirdparty/libriscv/` is a separate copy from `weft-warp-loop`'s
(worth reconciling into one copy later - flagged, not acted on, since
that would be a code change, not a keep/delete one).

`jolt_physics` — kept per direct instruction, reversing the earlier
"no physics modules, simulation is server-authoritative" call below.
`weft-warp-loop`'s own authoritative simulation in `fanout-core` is
unaffected either way; this is presumably for local/cosmetic physics
the adapter itself wants (ragdolls, debris, cloth) that don't need to
be authoritative. `godot_physics_2d`/`godot_physics_3d` (the built-in
alternative physics backends) stay deleted — Jolt is the sole physics
backend kept.

`sqlite` — kept as `open_telemetry`'s real build dependency (its
`SCsub` prepends `modules/sqlite/{thirdparty,src}` to its include
path), not evaluated independently. 9.6M on its own, the single
largest module kept.

`open_telemetry` — kept per direct instruction.

`astcenc`, `basis_universal`, `bcdec`, `betsy`, `cvtt`, `etcpak` —
kept per direct instruction (the GPU texture compression/transcoding
cluster: ASTC, Basis Universal supercompression, BC/DXT decode
(`bcdec`), a compute-shader-based BC/ETC encoder (`betsy`), a
BC6/BC7-focused encoder (`cvtt`), and a fast ETC1/ETC2 encoder
(`etcpak`) - the runtime texture-decoding side of what a real 3D
client needs regardless of whether it's also doing asset import).
Pulled their real `thirdparty/` deps in alongside them: root-level
`thirdparty/astcenc`, `thirdparty/basis_universal`,
`thirdparty/cvtt`, `thirdparty/etcpak` (checked each module's own
`SCsub` for its actual thirdparty path rather than assuming which
ones need one - `bcdec` and `betsy` don't reference an external
`thirdparty/` tree, so nothing extra was fetched for them).
`basis_universal`'s `config.py` also declares a `tinyexr` dependency,
but only `if env.editor_build` (encoder-side, editor/import-time
only) - not applicable to a non-editor runtime build, so `tinyexr`
was deliberately not pulled in for this.

`http3` was tried and dropped again: its `SCsub` depends on
root-level `thirdparty/picoquic`, `thirdparty/picotls`,
`thirdparty/mbedtls`, and `thirdparty/webtransportd` (the exact same
libraries `weft-warp-loop`'s own `flow-toolchain/thirdparty` already
vendors for its zone server) - genuinely tempting for protocol
consistency, but the adapter's own networking design (ADR 0050) reuses
`fanout_load_client`'s existing C++ QUIC/ZPB connection code directly,
not a Godot module, so this stays out. Not copied into this tree at
all (not even build-flag-disabled) - if reconsidered later, it needs
re-fetching root `thirdparty/{picoquic,picotls,mbedtls,webtransportd}`
alongside it, since none of those are otherwise present here.

Deleted (all the rest): `bmp`, `camera`, `cassie`, `csg`, `dds`,
`enet`, `fbx`, `gdscript`, `gltf`, `godot_physics_2d`,
`godot_physics_3d`, `gridmap`, `hdr`, `http3`, `interactive_music`,
`jpg`, `jsonrpc`, `keychain`, `ktx`, `lasso`,
`lightmapper_rd`, `mbedtls`, `meshoptimizer`, `mobile_vr`, `mono`,
`mp3`, `msdfgen`, `multiplayer`, `multiplayer_fabric`,
`multiplayer_fabric_asset`, `native_media`, `navigation_2d`,
`navigation_3d`, `noise`, `objectdb_profiler`, `ogg`,
`openxr`, `raycast`, `speech`, `text_server_fb`,
`tga`, `theora`, `tinyexr`, `upnp`, `vhacd`, `visual_shader`, `vorbis`,
`webp`, `webrtc`, `websocket`, `webxr`, `xatlas_unwrap`, `xr_grid`,
`zip`.

Reasoning: no Godot-own networking (`enet`, `websocket`, `webrtc`,
`multiplayer`, `upnp`, `http3`) — this adapter's own connection reuses
`fanout_load_client`'s existing QUIC/ZPB code directly, not Godot's
networking stack. No `mono` (C#) — not used. No `gdscript` —
`weft-warp-loop` already has its own scripting system (the s7 Lisp-1
sandboxed in libriscv, ADR 0006/0011/0028), now reachable through the
kept `sandbox` module instead. No VR/XR modules (`openxr`, `webxr`,
`mobile_vr`, `xr_grid`) — not in scope for this pass. This org's other
own additions (`cassie`, `multiplayer_fabric*`, `lasso`, `keychain`,
`native_media`, `speech`) are excluded for now too — real candidates
for later, not yet justified here.

## `thirdparty/` — kept only

- `astcenc`, `basis_universal`, `cvtt`, `etcpak` — the real build
  dependencies of the modules kept above, nothing else. Not a general
  `thirdparty/` checkout; each entry here exists only because a kept
  module's own `SCsub` references it directly.

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
