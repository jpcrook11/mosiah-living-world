# The Living Lands of Mosiah

An interactive, illustrated study companion for the **Book of Mosiah** (Book of Mormon).
Move through twenty scenes — from Zeniff's colony and King Noah's tower to Abinadi,
the waters of Mormon, the bondage and deliverance of Limhi's and Alma's peoples, and
the gathering in Zarahemla — as an isometric diorama or a walkable first/third‑person world.

**▶ Live site:** https://jpcrook11.github.io/mosiah-living-world/

## Features
- Twenty cinematic scenes with period‑appropriate mood, light, and crowds.
- A walk mode: explore each scene on foot (WASD / arrows / on‑screen joystick), look around by dragging.
- Talk to anyone — heroes speak scene‑specific, scripture‑grounded lines; townsfolk react to the moment; (and yes, you can greet the sheep).
- Trade with merchants in Lehi‑Nephi and Zarahemla using Nephite *senum* coins.
- Minimap, landmark notes, and a scene timeline/scrubber.

## Tech
Single self‑contained `index.html`. Browser Canvas2D + vanilla JavaScript, no build step
and no dependencies (other than Google Fonts over HTTPS). Just open it, or host it anywhere static.

## Local preview
Open `index.html` in any modern browser, or:
```
python3 -m http.server 8000   # then visit http://localhost:8000
```
