# AlphaRing - thejackbitt fork

>
> **Based on:** JackBitt's AlphaRing v1.2.1 (commit `bdad7eb`)
>
> For the original project, see [WinterSquire/AlphaRing](https://github.com/WinterSquire/AlphaRing)

---

## What's New in This Fork

### Features Added

#### 1. Controller-to-Player Binding (Splitscreen)
- Each player now has a **"Bind" button** next to the controller dropdown
- Click "Bind" → Press any button on a controller → Automatically assigns that controller to the player
- No more guessing which controller is "Controller 1" vs "Controller 2"

#### 2. Button-to-Action Binding (Gamepad Mapping)
- Each action in the Gamepad Mapping section has a **"Bind" button**
- Click "Bind" → Press a button → That button is assigned to the action

#### 3. Fixed Default Gamepad Mappings
- **Bug fixed:** Previously, all actions defaulted to "Left Trigger" due to uninitialized memory
- **Now:** New profiles initialize with standard Xbox Halo controls:

| Action | Button |
|--------|--------|
| Jump | A |
| Melee | B |
| Action/Interact | X |
| Change Weapon | Y |
| Reload | RB |
| Switch Grenades | LB |
| Shoot | RT |
| Throw Grenade | LT |
| Flashlight | D-pad Up |
| Crouch | Left Stick Click |
| Zoom | Right Stick Click |

#### 4. Controller Diagnostics (Splitscreen)
- The Splitscreen window now shows a live **XInput port readout** (ports 0-3, connected vs. disconnected, and whether a port is receiving input)
- It also lists the game's **input device objects** (`Game devices:`) and the XInput port (`input_user`) each one polls, plus each player's current port assignment
- Useful for figuring out why a second controller isn't detected or isn't controlling its player

---

## CI Builds & Releases

Every push builds `WTSAPI32.dll` on a **Windows GitHub Actions runner** (MSVC + vcpkg for SDL2/SDL2_mixer) and uploads it as the `WTSAPI32` artifact:

```bash
# download the latest artifact from the Actions page, or from your Deck/Arch box:
gh run download <run-id> -R el-mango-cosmico/AlphaRing -n WTSAPI32
```

Tagging a version (e.g. `v0.2-beta`) publishes a **beta Release** with the DLL attached:

```bash
git tag v0.2-beta && git push origin v0.2-beta
gh release download -R el-mango-cosmico/AlphaRing --pattern WTSAPI32.dll
```

Copy `WTSAPI32.dll` into the MCC `mcc/binaries/win64/` folder of your Steam library to install.

---

## Original Alpha Ring

A Modding Tool for MCC

[![Build status](https://ci.appveyor.com/api/projects/status/o3qbtc7jirw81xmb?svg=true)](https://ci.appveyor.com/project/WinterSquire/alpharing)
[![](https://dcbadge.limes.pink/api/server/https://discord.gg/TUyAnCrpuz)](https://discord.gg/TUyAnCrpuz)

### Showcase

| | |
|--|--|
| Camera Tool (H3) <br> ![Camera](https://github.com/WinterSquire/AlphaRing/assets/135317392/d359b2e8-5302-430f-be0d-bc065e63f546) | Object Browser (H3) <br> ![Object](https://github.com/WinterSquire/AlphaRing/assets/135317392/0bce1af7-354f-4d9d-92f7-eb2d46d8ae37) |
| 8 Players Campaign <br> ![Splitscreen 8 players](https://github.com/WinterSquire/AlphaRing/assets/135317392/7d9f4281-892a-47e2-8e0c-845a965e5d11) | Splitscreen With [Mod](https://steamcommunity.com/sharedfiles/filedetails/?id=3153235187) (By [Priception](https://steamcommunity.com/id/priception)) <br> ![H4](https://github.com/WinterSquire/AlphaRing/assets/135317392/5359868c-c5db-4300-9805-84c61b0bd8ee) |

### Features
* Splitscreen (all games)
* Camera Tool (H3)
* Object Browser (H3)

### Installation
Make sure you have the latest [Microsoft Visual C++ Redistributable](https://aka.ms/vs/17/release/vc_redist.x64.exe) installed.

Download the latest build from the [Releases](https://github.com/el-mango-cosmico/AlphaRing/releases) page (or a CI artifact, see [CI Builds & Releases](#ci-builds--releases)).

Place the DLL into the "Halo The Master Chief Collection\mcc\binaries\win64" directory and launch the game with EAC off.

For Running on Steam Deck/Linux, add the following command in the Steam Game Launch Options:
```
WINEDLLOVERRIDES="WTSAPI32=n,b" %command%
```

#### Batocera Linux / Steam Deck

Works with **any Proton version** (Proton 9.0, Proton Experimental, Proton GE, etc.)

1. **Configure the game**:
   - Right-click MCC → Properties → Compatibility
   - Enable "Force the use of a specific Steam Play compatibility tool"
   - Select any Proton version (Proton 9.0, Experimental, or Proton GE all work)

2. **Set launch options**: Add the following to Steam Launch Options:
   ```
   WINEDLLOVERRIDES="WTSAPI32=n,b" %command%
   ```

3. **Controller Setup (Important for non-Xbox controllers)**:
   - For 8BitDo and other third-party controllers, enable **Steam Input** for the controller
   - Go to Steam → Settings → Controller → Enable "Xbox Configuration Support"
   - This allows Steam to translate your controller inputs to XInput, which MCC and AlphaRing expect
   - Without this, some buttons (like A or stick clicks) may not be detected

> **Note:** Tested on Batocera Linux with the unofficial Batocera add-ons Steam client.

### Usage
Toggle menu: `F4` or `Controller Back` + `Controller Start`

To navigate using Controller use the `Right Stick` to move the mouse and `RB` to click.

When the menu is open, game input is disabled.

---

## Building from Source

> Most users don't need this — the CI pipeline builds the DLL automatically.
> See [CI Builds & Releases](#ci-builds--releases).

### Prerequisites
- Visual Studio 2022 Build Tools
- CMake 3.27+
- vcpkg with `sdl2` and `sdl2_mixer` installed (`x64-windows-static-md`):

  ```
  vcpkg install sdl2 sdl2-mixer --triplet x64-windows-static-md
  ```

### Build Commands
```bash
# First time setup
mkdir build && cd build
cmake .. -G "Visual Studio 17 2022" -A x64 -DCMAKE_TOOLCHAIN_FILE=C:/path/to/vcpkg/scripts/buildsystems/vcpkg.cmake -DVCPKG_TARGET_TRIPLET=x64-windows-static-md

# Build
"C:/Program Files (x86)/Microsoft Visual Studio/2022/BuildTools/MSBuild/Current/Bin/MSBuild.exe" WTSAPI32.vcxproj -p:Configuration=Release -p:Platform=x64
```

Output: `build/Release/WTSAPI32.dll`

> **Note:** The prebuilt libraries under `lib/*/lib/release/` are MSVC COFF import libraries,
> so MinGW on Linux cannot link this project — use MSVC (or the CI runner).

---

## Credits
- **Original AlphaRing:** [WinterSquire](https://github.com/WinterSquire/AlphaRing)
- **Controller Binding and Proton Fixes Fork:** [kirklandsig](https://github.com/kirklandsig/AlphaRing)
- [Assembly](https://github.com/XboxChaos/Assembly) for the tag group research.
- [Blender](https://github.com/blender/blender) for the bezier curve calculation.
- [Priception](https://github.com/Priception) for adding UI controller support and helping with the interface and crash issue.
