# Squeeze & Scrape

A digital version of the "squeeze and scrape" paint craft: dot puffy paint onto a card, then pull a ruler through it so the smears turn into a picture (a layered birthday cake, jellyfish, a campfire, or whatever you make up).

Open `index.html` in any modern browser. Everything is in that one file, with no build step or dependencies (fonts come from Google Fonts and fall back to system fonts when offline).

## How to play

- **Squeeze**: pick a colour, then hold on the card to squeeze a dot (it grows the longer you hold) or drag to pipe a line. Pause mid-line to build up a blob.
- **Scrape**: press anywhere on the card and drag. The ruler moves only along its own normal, like a real ruler, and pushes paint the way you pull it. Stop and lift between layers for crisp edges. Turn the ruler with the angle presets or slider, and use a shorter scraper for local smears.
- **Card**: pick a template to get grey guide marks and pre-squeezed paint, then press **Scrape it for me** to watch it get pulled. Edit the message at the top of the card.
- **Make your own**:
  - **Trace a picture** puts any photo or drawing faintly on the card so you can squeeze paint over it. It is left out of the saved picture.
  - **Save as template** records everything squeezed since the last wipe, plus your ruler pulls, your message and your traced picture. It appears next to the built-in templates and is kept in this browser. **Squeeze it for me** and **Scrape it for me** replay it.
  - **Download template** saves it as a `.json` file to share. **Open template file** (or dropping a file onto the card) loads one. Template files are checked and clamped before use, so a broken or hostile file is rejected.
- **Undo**, **Wipe paint**, and **Save picture** (PNG).

Keyboard: `S` squeeze, `R` ruler, `Q`/`E` turn the ruler, `[`/`]` nozzle size, `Ctrl/Cmd+Z` undo.

## Template files

```json
{
  "kind": "squeeze-and-scrape-template",
  "version": 1,
  "name": "For Sam",
  "message": "For Sam",
  "items": [
    { "t": "dot", "c": "red", "x": 150, "y": 190, "r": 10 },
    { "t": "line", "c": "yellow", "pts": [[80, 340, 6.2], [86, 342, 6.2]] }
  ],
  "scrape": [{ "a": 0, "len": "full", "x": 200, "y": 150, "d": 150 }],
  "picture": "data:image/jpeg;base64,..."
}
```

Coordinates are in card cells (400 × 600). `c` is a paint id (`red`, `orange`, `yellow`, `green`, `sky`, `blue`, `navy`, `grape`, `pink`, `white`, `cocoa`, `black`). Each line point is `[x, y, radius]`. Each scrape step gives the ruler angle `a`, length (`full`, `half` or `short`), where its centre starts (`x`, `y`) and how far it travels along its normal (`d`, negative pulls up or left). Replays do all the squeezes first, then the pulls. `picture` is optional.

## How the paint works

- The card is a 400 × 600 grid. Each cell stores a paint thickness and a colour in Kubelka–Munk K/S form, so colours mix like pigment (blue + yellow makes green, not grey).
- The ruler's edge is a row of samples. As it advances, each cell keeps a thin film (mostly its own colour) and the excess is pushed ahead in a bead.
- The bead keeps up to four colour layers instead of mixing them. Whatever was picked up last is laid down first, which is why a red dot above a yellow dot smears out as red, then yellow, then red again, just like the real craft.
- Uneven pressure along the edge (layered value noise) leaves the streaks. Lifting the ruler drops the bead as a ridge.
- Rendering adds gloss and a soft shadow from the height field, then composites the paint over a paper texture drawn at screen resolution.
