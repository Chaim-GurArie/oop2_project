# Jetpack Joyride (C++ / SFML)

A C++ clone of *Jetpack Joyride*, built with [SFML 3.0](https://www.sfml-dev.org/) as an object-oriented programming course project. The player controls a runner wearing a jetpack, dodging lasers and missiles, collecting coins, and grabbing power-ups while the level scrolls endlessly.

## Gameplay

- **Space** — hold to thrust upward with the jetpack; release to fall.
- **Escape** — go back / pause, depending on the current screen.
- Avoid **lasers** and **missiles**, collect **coins**, and pick up **power-ups** (speed boost, super-power runner mode) to survive as long as possible and beat the high score.

Screens: main menu, rules/help, gameplay, game over, and a high score board.

## Requirements

- **CMake** ≥ 3.26
- A C++23 compiler with MSVC toolset (project is configured for MSVC `/std:c++23`; Clang is also supported by [cmake/CompilerSettings.cmake](cmake/CompilerSettings.cmake))
- **SFML 3.0.0**, expected at `C:/SFML/SFML-3.0.0` (see `SFML_LOCATION` in [CMakeLists.txt](CMakeLists.txt)) — adjust this path if your SFML install lives elsewhere
- Windows (the project relies on MSVC-specific settings such as `VS_DEBUGGER_WORKING_DIRECTORY`)

## Building

The project ships CMake presets ([CMakePresets.json](CMakePresets.json)) for both VS Code (CMake Tools) and Visual Studio:

```powershell
cmake --preset x64-Debug
cmake --build --preset x64-Debug
```

Or open the folder directly in VS Code / Visual Studio and let the CMake integration pick up the presets automatically. Two configurations are available: `x64-Debug` and `x64-Release`.

Resources (textures, sounds, fonts) are copied automatically to the build output directory as a post-build step, so the executable can be run directly from `out/build/<preset>/Debug/`.

### Debug builds and AddressSanitizer

Debug builds compile with `-fsanitize=address` ([CMakeLists.txt](CMakeLists.txt)). The build automatically copies the matching ASan runtime DLL (`clang_rt.asan_dynamic-x86_64.dll`) next to the executable, so it will run standalone — no need to launch exclusively through the IDE debugger.

## Running

After building, run the executable from the build output folder (e.g. `out/build/x64-Debug/Debug/oop1_project.exe`), or launch/debug it directly from VS Code or Visual Studio.

## Project structure

- [`src/`](src/) / [`include/`](include/) — game source and headers
  - `Controller`, `InputHandler` — own the window and the main game loop, manage a stack of `Screen`s, and translate raw input into game actions
  - `Screen` and its subclasses (`MenuScreen`, `GamePlayScreen`, `GameOverScreen`, `RulesScreen`, `HighScoreScreen`) — the different game states/UIs
  - `GameSession`, `Board`, `LevelGenerator`, `CoinFormation`, `GameObjectFactory` — gameplay orchestration, procedural level generation, and entity creation
  - `Object` / `StaticGameObject` / `MovingGameObject` and subclasses (`Player`, `Coin`, `Laser`, `Missile`, `Scientist`, `PowerUpBox`, `SpeedItem`, `Exhaust`, ...) — game entities
  - `PlayerState` and subclasses (`WalkState`, `JumpState`, `BoostState`, `DeadState`, `SuperPowerRunnerState`, `LaserState`/`LaserStaticState`/`LaserRotatingState`) — state machine driving player and laser behavior
  - `CollisionManager`, `ObjectCleaner` — collision detection and off-screen object cleanup
  - `Resources`, `AssetLoader`, `AudioManager`, `BackgroundSystem` — asset loading, playback, and scrolling background rendering
  - `HUD`, `Button` — UI elements
  - `GameException` — custom exception type for game-level error handling
- [`resources/`](resources/) — textures, sounds, fonts, and the high score file
- [`cmake/`](cmake/) — compiler settings, SFML setup, and packaging (zip) helpers

## Known issues

- Running a Debug build's `.exe` directly (outside the IDE) previously failed with `STATUS_DLL_NOT_FOUND` because the AddressSanitizer runtime DLL wasn't next to the executable. This is now fixed by an automatic post-build copy step in [CMakeLists.txt](CMakeLists.txt).
