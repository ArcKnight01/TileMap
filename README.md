# TileMap

A map creator built in C++ with SFML (Visual Studio).

Originally created for my EZ-Shop startup as a way to map a grocery store for customer navigation, I revamped it into a general map creator -- click tiles to build walls, mazes, or arenas, save and load configurations, and control a character within the map. Handy for RPG-style games.

Created September 2022.

> **Continued in [AStarTileMap](https://github.com/ArcKnight01/AStarTileMap).** That repo is this code moved to CMake (it runs on
> Linux and in VS Code), with the A\* path planning this project was meant to have, the merged navigation graph, and clearer
> tile highlights. This repo is kept as the original Visual Studio version.

---

## Running it
Open `TileMap.sln` in Visual Studio and run. The program first asks **in the console** for a map name (`a`, `default`,
`dungeon`, `Unamed_Map`, or Enter for `default`), then opens the window. Textures and maps are loaded from the `TileMap/`
project folder.

## Controls
| Input | Effect |
|---|---|
| **Left click** | Select a tile |
| **Space** | On the selected tile: ground ↔ wall (a start or end tile turns back into ground) |
| **Right click** | Select a tile and toggle it at once |
| **Enter** | On the selected ground tile: place the start, then the end. On a start or end tile: remove it. |
| **W A S D** | Move the character |
| **E** | Save the map to `<name>.txt` and close |
| **Esc** | Quit without saving |

---

## How the code works

```
TileMap/
  TileMap.cpp        main(): window, input, game loop
  Tile.h/.cpp        class Board: the 20×20 grid, drawing, editing, load/save, pathfinding() stub
  character.h/.cpp   class Character: the player moved with WASD
  stdafx/targetver   Visual Studio template leftovers
  Textures/          tile sprites; *.txt saved maps
```

**`main()`**: opens a 1000×1000 window with a 20×20 `Board` (50 px tiles), asks for the map name (`importBoard`), and spawns
the `Character` on the start tile. Every frame it reads input into flags on the board or the character, calls
`convertTile()` to apply edits, then draws the board and the character.

**`Board` (Tile.h/.cpp)**
- `tileTypes`: one `tiletype` per tile (`wall`, `ground`, `startnode`, `endnode`, `checkpoint`). Tile `i` is at column `i % 20`, row `i / 20`.
- `getTileN(x, y)` converts pixels to a tile number; `getTilePos(i)` converts a tile number to pixels.
- `convertTile()` applies Space (wall/ground) and Enter (start/end) to the selected tile.
- `drawBoard()` draws each tile's sprite plus the selection and hover colours.
- `importBoard()` / `exportBoard()` load and save `<name>.txt`: 400 numbers, one per line (`0` ground, `1` wall, `2` start, `3` end).
- `pathfinding()` is an unfinished A\* stub: it only adds the start to the open list. AStarTileMap implements it.

**`Character`**: moves with velocity and friction, and `updateNavigation()` stops it at walls by checking the 8 tiles around it.
It marks the tile it's standing on as a yellow `checkpoint`.
