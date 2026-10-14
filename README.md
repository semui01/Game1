# Squeeze & Scrape

A digital version of the "squeeze and scrape" paint craft: dot puffy paint onto a card, pull a ruler through it so the smears turn into a picture, then finish it with paint-pen line work (a layered birthday cake, jellyfish, a campfire, or whatever you make up).

Open `index.html` in any modern browser. Everything is in that one file, with no build step or dependencies (fonts come from Google Fonts and fall back to system fonts when offline).

## How to play

- **Squeeze**: pick a colour, then hold on the card to squeeze a dot (it grows the longer you hold) or drag to pipe a line. Pause mid-line to build up a blob.
- **Scrape**: press anywhere on the card and drag. The ruler moves only along its own normal, like a real ruler, and pushes paint the way you pull it. Stop and lift between layers for crisp edges. Turn the ruler with the angle presets or slider, and use a shorter scraper for local smears.
- **Draw**: finish the card with a paint pen (ink, white gel, gold or cherry; fine, medium or bold) on top of the paint. Lines thin out a little when you move fast. On a template card, the card's own line work shows as faint dashes you can trace.
- **Card**: pick a template to get grey guide marks and pre-squeezed paint. **Do it for me** runs the three steps (**1 Squeeze**, **2 Scrape**, **3 Draw**), and the next step is highlighted. Each built-in card has its own line art: candle flames, piped frosting and polka dots on the cake; faces, curly tentacles and bubbles on the jellyfish; a moon, stars and log rings for the campfire. Edit the message at the top of the card.
- **Make your own**:
  - **Trace a picture** puts any photo or drawing faintly on the card so you can squeeze paint over it. It is left out of the saved picture.
  - **Save as template** records everything squeezed since the last wipe, plus your ruler pulls, your pen lines, your message and your traced picture. It appears next to the built-in templates and is kept in this browser. **Do it for me** replays it.
  - **Download template** saves it as a `.json` file to share. **Open template file** (or dropping a file onto the card) loads one. Template files are checked and clamped before use, so a broken or hostile file is rejected.
- **Shared gallery** (only in the Claude-hosted version): **Post to gallery** shares your card with everyone who opens the page. The gallery under the card lists everyone's posts live, and tapping one loads it to play. Anyone who can open the page can browse and play. Posting needs Contributor access or above. People can remove their own posts, and the page's owner can remove any post. Each person can have up to 12 posts.
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
  "lines": [{ "c": "gold", "pts": [[60, 140, 2.4], [67, 143, 2.2]] }],
  "picture": "data:image/jpeg;base64,..."
}
```

Coordinates are in card cells (400 × 600). `c` is a paint id (`red`, `orange`, `yellow`, `green`, `sky`, `blue`, `navy`, `grape`, `pink`, `white`, `cocoa`, `black`). Each line point is `[x, y, radius]`. Each scrape step gives the ruler angle `a`, length (`full`, `half` or `short`), where its centre starts (`x`, `y`) and how far it travels along its normal (`d`, negative pulls up or left). Each pen stroke in `lines` has a pen id `c` (`ink`, `white`, `gold`, `cherry`) and points `[x, y, width]`. Replays do all the squeezes first, then the pulls, then the pen lines. `lines` and `picture` are optional.

## Shared gallery storage

The Claude-hosted page declares the `db` capability with these rules:

```json
[
  { "path": "posts", "read": "view", "write": "owner" },
  { "path": "posts/{self}", "write": "interact" }
]
```

- `posts/<user id>` is that person's index, `{ posts: [{ id, name, thumb, at }] }`. Each `thumb` is a 120 × 180 JPEG data URL. The gallery subscribes to this collection.
- `posts/<user id>/full/<post id>` holds the full template, in the same format as a template file. It is fetched only when someone plays the card.
- Everything read back is validated like an uploaded template file and shown with `textContent`.

On a plain web page (GitHub Pages, or opening `index.html` directly) the gallery is hidden, and templates are shared as files.

## How the paint works

- The card is a 400 × 600 grid. Each cell stores a paint thickness and a colour in Kubelka–Munk K/S form, so colours mix like pigment (blue + yellow makes green, not grey).
- The ruler's edge is a row of samples. As it advances, each cell keeps a thin film (mostly its own colour) and the excess is pushed ahead in a bead.
- The bead keeps up to four colour layers instead of mixing them. Whatever was picked up last is laid down first, which is why a red dot above a yellow dot smears out as red, then yellow, then red again, just like the real craft.
- Uneven pressure along the edge (layered value noise) leaves the streaks. Lifting the ruler drops the bead as a ridge.
- Rendering adds gloss and a soft shadow from the height field, then composites the paint over a paper texture drawn at screen resolution.
- Pen lines are vector strokes on their own layer above the paint, smoothed with midpoint curves. The ruler doesn't move them. The built-in line art gets a small seeded wobble and width variation so it looks hand drawn.
