# The Living Lands of Mosiah

An interactive, illustrated study companion for the **Book of Mosiah** (Book of Mormon).
Move through twenty scenes — from Zeniff's colony and King Noah's tower to Abinadi,
the waters of Mormon, the bondage and deliverance of Limhi's and Alma's peoples, and
the gathering in Zarahemla — as an isometric diorama or a walkable first/third‑person world.

**▶ Live site:** https://jpcrook11.github.io/mosiah-living-world/

## Features
- **The Chronicle** — a cinematic retelling of all twenty scenes: letterboxed title cards, slow camera moves,
  a narration line and the scene's passage for each, crossfades between scenes, and optional spoken narration
  (uses your browser's own voices). Watch it in timeline order or in the order the book tells it.
- **Twenty living scenes** with sun‑cast shadows, firelight and lit windows at night, rain, ash, fireflies,
  drifting clouds and birds. Some scenes stage moments from the text: Abinadi's shining face (13:5), Helam in
  the water (18:12–14), Benjamin's tents with their doors toward the temple (2:6), the people slipping
  out with their flocks while the guards sleep (22:11; 24:19), and the angel's cloud and earthquake (27:11).
- **Walk on foot** (WASD/arrows, Shift to run, drag or capture the mouse to look, on‑screen joystick on touch)
  through towns with roofs, stepped temples and towers under a full 360° sky.
- **Talk to anyone.** Characters speak scripture‑grounded lines tied to their source verse, with portraits.
  Tap the reference to read the verse; every passage you hear is gathered into your journal.
- **The covenant at the waters of Mormon** (Mosiah 18:8–9) — eleven acts of service across the scenes:
  carry burdens, mourn with those that mourn, comfort those in need, and stand as a witness.
  Look for ✦. Trade for food with *senum* to answer the beggar's petition (4:16–19).
- **Book of Remembrance** — scenes witnessed, words gathered, deeds kept, and your satchel. Progress is saved
  in your browser.
- **Atlas** — a schematic map of the eight journeys in Mosiah (Zeniff's colony, Alma's flight, the lost
  forty‑three, Ammon's sixteen, both escapes, and more), and a timeline of the book's three threads showing
  how the chapters' order differs from the order of events.
- A procedural score and soundscape (Web Audio, no audio files), postcards of any view, shareable
  links (`#scene-6`), keyboard shortcuts (press **?**), and automatic quality scaling for slower devices.

Scripture quotations are from the public‑domain text of the Book of Mormon.

## Tech
Single self‑contained `index.html`. Browser Canvas2D + Web Audio + vanilla JavaScript, no build step
and no dependencies (other than Google Fonts over HTTPS). Just open it, or host it anywhere static.

## Local preview
Open `index.html` in any modern browser, or:
```
python3 -m http.server 8000   # then visit http://localhost:8000
```
