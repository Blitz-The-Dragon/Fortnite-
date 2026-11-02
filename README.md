# Fortnite-: OG Fortnite Reconstructions

An index of open-source projects that reconstruct, reimplement or serve classic ("OG") Fortnite: Rust rewrites, decompilation/reconstruction work, game servers and backends. Each project is a **Git submodule pinned to a verified commit** (snapshot taken 2026-11-02), so this repository holds only links and documentation, never anyone else's code or any game files.

```bash
git clone --recurse-submodules https://github.com/Blitz-The-Dragon/Fortnite-
# or, after a plain clone:
git submodule update --init --recursive
# pull the latest upstream commits later:
git submodule update --remote
```

> **Game files are not included and are not linked.** Every project here needs a specific Fortnite client build, which is Epic Games' copyrighted software. You must supply your own copy. Using private servers violates Fortnite's EULA; these projects are for preservation, research and education. Never run them against Epic's live services.

## Projects

| Path | Upstream | Language | What it is | Pinned commit |
|---|---|---|---|---|
| `rust/Rustmaker` | [Nexus-FN/Rustmaker](https://github.com/Nexus-FN/Rustmaker) | Rust | WebSocket matchmaker for Fortnite private servers | `ac26352` (2023-07-04) |
| `rust/FortniteRuntime-rs` | [omonkeyz/Fortnite-Rust](https://github.com/omonkeyz/Fortnite-Rust) | Rust | AI-assisted pure-Rust rewrite of the Fortnite **1.8** client runtime and asset pipeline | `d61109b` (2026-10-04) |
| `reconstruction/OT6-Reconstruction` | [ApfelTeeSaft/OT6-Reconstruction](https://github.com/ApfelTeeSaft/OT6-Reconstruction) | C++ | Decompiled game-server logic of the 2016 alpha (Online Test 6.5, UE 4.12) — documentation, does not compile | `3e34213` (2026-01-08) |
| `reconstruction/Limbo` | [ArielS-gif/Limbo](https://github.com/ArielS-gif/Limbo) | C++ | Makes the OT6.5 Save the World alpha playable (missions, 4-player co-op, outposts) | `90f6912` (2026-10-28) |
| `reconstruction/Limbo-Backend` | [ArielS-gif/Limbo-Backend](https://github.com/ArielS-gif/Limbo-Backend) | JavaScript | Backend for Limbo, based on vopswtf's Fortify-Alpha | `c100e3d` / v0.3.2 |
| `gameservers/Project-Reboot-3.0` | [Milxnor/Project-Reboot-3.0](https://github.com/Milxnor/Project-Reboot-3.0) | C++ | Widely used Chapter 1–2 game server (seasons 3–15) | `10c6590` (2026-10-01) |
| `backends/LawinServer` | [Lawin0129/LawinServer](https://github.com/Lawin0129/LawinServer) | JavaScript | Single-user backend (Epic services emulator) for BR and STW, all versions | `5fffbc9` (2026-11-01) |

### A note on "Rust-based OG Fortnite"
There is no complete Rust game *server* for OG Fortnite. The Rust ecosystem is two projects: **Rustmaker** (a matchmaker only, unmaintained since 2023) and **FortniteRuntime-rs** (a client-side runtime, ~40 crates, very active, built with Claude — see its `CLAUDE_HANDOFF.md`). Four GitHub repos named `Fortnite-Rust` / `Fortniterust` (`sonoma-unreal`, `lucidpp19-cpu`, `penally`, `Iker3329`) are copies of omonkeyz's initial commit with small additions (a web demo, a Dockerfile for `fn-inspect`); only the original is included.

### Excluded on purpose
- Repos advertising "V-Bucks generators", skin unlockers or cheat injection (for example `Agentbrapurges/fortnite-private-lobby-server`) — these are typical malware lures.
- Build-archive repos that link to downloads of Epic's game binaries.
- The AI-assisted shooter decompilation described in [momo5502's write-up](https://momo5502.com/posts/2026-10-09-game-decompilation/) is not Fortnite and its code is not public.

---

## Required external game files (checklist)

You provide these yourself, from your own copy. Build IDs are as each project states them.

**Rustmaker**
- [ ] None directly. It needs a running backend and game server to hand matches to (e.g. LawinServer + Project Reboot).

**FortniteRuntime-rs**
- [ ] Fortnite **1.8** install, `FortniteGame/Content/Paks/` (5 paks, pak v3, 238,484 entries). The paks are read, never modified.
- [ ] Optional: Fortnite **1.10** paks (pak v4) for the `--ue4-version 514` layout and `diff-index`.
- [ ] Exported support files the runtime still needs and does not yet generate itself: `T_Grasslands_D.png`, `dome_optimized.bin`, `sunmoon.bin`, plus an `asset-manifest.json` for `fn-export` (per upstream `CLAUDE_HANDOFF.md`, this is an open blocker).

**OT6-Reconstruction**
- [ ] None to read it. To go further you would need the OT6.5 alpha (UE **4.12**, 32-bit) client and Unreal Engine 4.12 source; upstream states the code will not compile without significant work.

**Limbo + Limbo-Backend**
- [ ] Fortnite OT6.5 build **2252016-3240987**, in a folder named `Fortnite (2252016-3240987)` (32-bit client, `FortniteClient-Win64-Shipping.exe` is x86 despite its name).
- [ ] Folder layout, side by side:
  ```
  OT6.5\
    Fortnite (2252016-3240987)\
    Limbo\
    Limbo-Backend\   (host only)
  ```

**Project Reboot 3.0**
- [ ] A Chapter 1–2 client build. Best supported: seasons 5–12; seasons 3–15 claimed; S1–S2 and S16–S19 unreliable.
- [ ] A launcher that starts the client as a server and injects the DLL (upstream uses the Reboot Launcher).
- [ ] A backend (e.g. LawinServer) for the clients.

**LawinServer**
- [ ] Any Fortnite build; you redirect Epic's service URLs to `localhost:3551` (upstream suggests Fiddler or an SSL-bypass/redirect DLL).

---

## Build & run instructions

### Toolchains at a glance

| Project | Toolchain | Key dependencies |
|---|---|---|
| Rustmaker | Rust stable (edition 2021), Cargo | `tokio` (full), `simple-websockets` 0.1.5, `uuid`, `chrono`, `md5`, `dotenv`, `owo-colors`; build-dep `winres` (Windows icon) |
| FortniteRuntime-rs | Rust stable (edition 2021, workspace resolver 2); Windows (DX12) or Vulkan GPU | `wgpu` 30.0.1 (dx12, vulkan, wgsl), `winit` 0.30, `naga` 30, `glam` 0.32, `image` 0.25, `gltf` 1.4, `cpal` 0.15, `lewton`, `iced-x86` 1.21, `object` 0.36, `memmap2`, `miniz_oxide`, `rayon`, `gilrs` 0.11, `fontdue` 0.9, `windows-sys` 0.52; vendored `opus-decoder` |
| OT6-Reconstruction | UnrealBuildTool layout (`*.Target.cs`, `*.Build.cs`) for UE 4.12 | None buildable; analysis was done in IDA Pro 9 + Hex-Rays |
| Limbo | Visual Studio 2022+, "Desktop development with C++", **x86** MSVC, C++17 | Win32 only; outputs `out/injector.exe` + `out/gameserver.dll` |
| Limbo-Backend | Node.js | `express`, `bcrypt`, `jsonwebtoken`, `dotenv-safe`, `pako`, `walkdir` |
| Project Reboot 3.0 | Visual Studio 2022 (toolset v143), C++ latest, x64 DLL | Vendored: MinHook, ImGui, curl, fmt, spdlog, nlohmann `json.hpp`, memcury |
| LawinServer | Node.js | `express`, `ws`, `ini`, `uuid`, `cookie-parser`, `xml-parser`, `xmlbuilder` |

### Rustmaker
```bash
cd rust/Rustmaker
# edit .env: set the port
cargo build --release
# Windows: set MATCHMAKER_IP=<public ip>:<port>, then
target/release/matchmaker.exe
# or Docker:
docker run -p <port>:<port> -e PORT=<port> ghcr.io/nexus-fn/matchmaker:main
```
Note: `Cargo.toml` declares `uuid` twice (1.1.2 and 0.8.2); if your Cargo version rejects the duplicate key, delete the `uuid = "0.8.2"` line.

### FortniteRuntime-rs
```bash
cd rust/FortniteRuntime-rs
# asset tooling only (pure Rust, no GPU needed; also builds on Linux):
cargo build --release -p fn-inspect
cargo test -p fn-pak -p fn-uasset            # verification suite
export FN_ROOT=/path/to/1.8/FortniteGame     # or pass --root
target/release/fn-inspect paks
target/release/fn-inspect sweep --report sweep.json

# the game binary (Windows, wgpu DX12/Vulkan):
cargo g        # alias: cargo run --profile fast -p fn-app --bin fortnite-rs
cargo gl       # lobby exe
cargo run --release -p fn-app -- --startup-capture --paks <Paks dir> --out verification/startup
```
Upstream's README hard-codes a personal `CARGO_TARGET_DIR`; ignore it.

### OT6-Reconstruction
Nothing to build. Read `FortniteGame/` (gameplay) and `NetworkingRebuild/` (UE 4.12 replication internals such as RepLayout) as documentation.

### Limbo + Limbo-Backend
```bat
:: Windows, Visual Studio 2022+ with C++ workload
cd reconstruction\Limbo
build.bat                         :: finds VS via vswhere, compiles x86 with /std:c++17 /MT
:: -> out\injector.exe, out\gameserver.dll
```
Lay the folders out as in the checklist, then run `Play Limbo.bat` (installs Node.js on first host run and runs `npm install` for the backend). Hosting needs TCP 3551 and 5222 and UDP 3551 open. To run the backend alone:
```bash
cd reconstruction/Limbo-Backend
npm install
cp .env.example .env    # set PORT=3551 and a long random JWT_SECRET
node index.js
```
Prebuilt, CI-signed zips with SHA-256 are on [Limbo's Releases](https://github.com/ArielS-gif/Limbo/releases) and [Limbo-Backend's Releases](https://github.com/ArielS-gif/Limbo-Backend/releases).

### Project Reboot 3.0
```bat
:: Windows: open "Project Reboot 3.0.sln" in Visual Studio 2022, Release | x64, Build
msbuild "Project Reboot 3.0\Project Reboot 3.0.vcxproj" /p:Configuration=Release /p:Platform=x64
```
Linux cross-build: install [msvc-wine](https://github.com/mstorsjo/msvc-wine), then `./build-linux.sh` (output in `Build/`). Run the resulting DLL through the Reboot Launcher. The project's Discord moved to `discord.gg/rebootmp`; the old `discord.gg/reboot` is no longer theirs.

### LawinServer
```bash
cd backends/LawinServer
npm install          # or install_packages.bat
npm start            # or start.bat -> "Started listening on port 3551"
```
Settings and in-game events are in `Config/config.ini`.

### A typical local Chapter 1 stack
1. LawinServer (backend, port 3551) →
2. Project Reboot game server DLL in a server instance of the client →
3. Optional: Rustmaker to queue players into that server →
4. Clients redirected to the backend.

---

## Licenses
Each submodule keeps its own license: Rustmaker MIT, Limbo MIT, OT6-Reconstruction MIT (documentation; reconstructed code is described upstream as © Epic Games), Project Reboot BSD-3-Clause, LawinServer GPL-3.0. FortniteRuntime-rs and Limbo-Backend state no license file at the pinned commit — treat them as all rights reserved unless the authors say otherwise. Fortnite and Unreal Engine are trademarks of Epic Games, Inc.; none of these projects is affiliated with Epic.
