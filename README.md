# Squeeze & Scrape

A digital version of the "squeeze and scrape" paint craft: dot puffy paint onto a card, then pull a ruler through it so the smears turn into a picture (a layered birthday cake, jellyfish, a campfire, or whatever you make up).

Open `index.html` in any modern browser. Everything is in that one file, with no build step or dependencies (fonts come from Google Fonts and fall back to system fonts when offline).

## How to play

- **Squeeze**: pick a colour, then hold on the card to squeeze a dot (it grows the longer you hold) or drag to pipe a line. Pause mid-line to build up a blob.
- **Scrape**: press anywhere on the card and drag. The ruler moves only along its own normal, like a real ruler, and pushes paint the way you pull it. Stop and lift between layers for crisp edges. Turn the ruler with the angle presets or slider, and use a shorter scraper for local smears.
- **Card**: pick a template to get grey guide marks and pre-squeezed paint, then press **Scrape it for me** to watch it get pulled. Edit the message at the top of the card.
- **Undo**, **Wipe paint**, and **Save picture** (PNG).

Keyboard: `S` squeeze, `R` ruler, `Q`/`E` turn the ruler, `[`/`]` nozzle size, `Ctrl/Cmd+Z` undo.

## How the paint works

- The card is a 400 × 600 grid. Each cell stores a paint thickness and a colour in Kubelka–Munk K/S form, so colours mix like pigment (blue + yellow makes green, not grey).
- The ruler's edge is a row of samples. As it advances, each cell keeps a thin film (mostly its own colour) and the excess is pushed ahead in a bead.
- The bead keeps up to four colour layers instead of mixing them. Whatever was picked up last is laid down first, which is why a red dot above a yellow dot smears out as red, then yellow, then red again, just like the real craft.
- Uneven pressure along the edge (layered value noise) leaves the streaks. Lifting the ruler drops the bead as a ridge.
- Rendering adds gloss and a soft shadow from the height field, then composites the paint over a paper texture drawn at screen resolution.
