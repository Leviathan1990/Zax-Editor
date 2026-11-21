Zax Editor - Unofficial Map Tool
================================

A free map editor for the game's .zax maps, for Windows and Linux. It works with
the demo and with the full version of the game. See Legal.txt before you use or
share it.


REQUIREMENTS
------------
- An installed copy of the game (demo or full version). The editor contains no
  game files: it reads your installation.
- Nothing else. The game does not have to run while you edit.


FIRST START
-----------
1. Start Zax Editor.
2. File > Set Game Folder... and choose the folder that holds the game's
   executable (Zax.exe) and its Levels, Cache and Textures folders.
   Opening a map from the game's Levels folder sets it automatically.
3. The first time, the editor reads the map format from the game program
   (a few seconds, a progress window is shown). This is stored and reused.

Always work on copies of maps, or keep a backup of the Levels folder.


MAIN WINDOW
-----------
- Map view (centre): the ground, the sprites and the editor markers.
  Mouse wheel = zoom, middle button = scroll.
- Objects / Assets (left): the objects of the map, and every placeable object
  found in the game's maps, with pictures.
- Properties / Terrain (right): the values of the selected object, and the
  ground painting tools.
- Messages (bottom): Issues (what the game would reject) and Console.


EDITING
-------
Objects
- Click an object to select it; drag it to move it.
- Drag on empty ground (or Shift + drag anywhere) to select with a rectangle;
  Ctrl + click adds or removes an object.
- Dragging one selected object moves the whole selection.
- Copy / paste (Ctrl+C / Ctrl+V) works between maps.
- Duplicate (Ctrl+D), Delete (Del), Select All (Ctrl+A).
- Assets: drag an asset onto the map, or double click it.
- Insert Object (Ctrl+I): any object found in the game's maps.

Values
- Double click a value in Properties to change it. Where the game has a list of
  allowed values (teams, render types, models, sequences, ...) a drop-down list
  helps; any value can still be typed.
- Right click array items (e.g. Activity, Action) to add, duplicate or remove
  them, or to change the class of an embedded object. Classes without an example
  in the game's maps are offered with the game's default values
  ("game defaults").

Polygons
- Select a polygon to see its corner handles.
- Drag a handle to move a corner, double click an edge to add a corner,
  right click a corner to delete it, drag the inside to move the polygon.

Ground (Terrain tab)
- Paint Terrain (T): paint the texture level or the pattern seed of the
  ground with the brush.
- Pick a texture from the list to paint it.
- Add / Replace / Remove Last change the map's ground textures.

Grid
- View > Snap to Grid (G) makes moved objects land on the grid.

Animation
- View > Animate Sprites plays the looping animations of the game's objects
  (torches, water, machines ...) as the game does. Turn it off for a still view.

New map
- File > New Map... (Ctrl+N): size, ground texture and a map of the game to take
  the general settings and the player's start point from.

Saving
- Validate (F7) lists anything the game would reject.
- Save (Ctrl+S). Every edit can be undone (Ctrl+Z) until you close the map.
- Way points and mini-map: the game builds a map's way points (<map>.way) and
  mini-map (<map>.tga) only when they are missing. When you change the
  obstacles of a map, the editor offers to rename the outdated files to *.bak
  so the game rebuilds them. The game's own maps also have their original way
  points in DATA.dat; save such a map under a new name to get new ones.


SHORTCUTS
---------
Ctrl+N new map        Ctrl+O open           Ctrl+S save         F7 validate
Ctrl+Z undo           Ctrl+Y / Ctrl+Shift+Z redo
Ctrl+C copy           Ctrl+V paste          Ctrl+D duplicate    Del delete
Ctrl+A select all     Ctrl+I insert object  Ctrl+0 fit map      T paint terrain
G snap to grid


TROUBLESHOOTING
---------------
- "Class schema not available": the game folder is wrong; choose the folder
  that contains Zax.exe.
- A map does not load in the game: run Validate (F7) and check the Issues tab.
- The Console tab shows what the editor did (loading, saving, warnings).


CREDITS
-------
See Legal.txt and THIRD_PARTY_NOTICES.txt.
