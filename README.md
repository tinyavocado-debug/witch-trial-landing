# Witch Trial: landing page

The website for [Witch Trial](https://github.com/tinyavocado-debug/witch-trial), a social-deduction party game for 5–12 players on their phones. It's for people who have just heard of the game and want to know if it's worth playing.

As you scroll, the camera walks through the village at night (the gate, the lane, the bonfire, the courthouse, the graveyard, the chapel) and ends on the accusation: "The village has accused you." A separate [How to Play](how-to-play/) page explains the rules.

It's plain HTML, CSS and JavaScript with no build step.

## Running it locally

```bash
python3 -m http.server 8080
```

Open http://localhost:8080/. The page has to be served; opening `index.html` from disk won't load its scripts. Browsers cache Python's server aggressively, so after changing a file use a hard reload (Cmd+Shift+R).

## Linking to the game

The Host a Game and Join a Village buttons go to the game's address in `<meta name="game-url">`. It's set in two places, which must match:
- `index.html`
- `how-to-play/index.html`

Empty means the site is served by the game itself.

## How it's put together

| Path | What it is |
|---|---|
| `index.html`, `styles.css`, `tokens.css` | The scroll-through page, its layout and its design tokens |
| `camera.js`, `stops.js` | The scroll camera: one video per stop, scrubbed by scroll position. `stops.js` sets each stop's length. |
| `main.js`, `links.js` | Page setup, the final statement, and links into the game |
| `video/`, `images/` | One 1280×720 clip per stop, and its first frame as a poster |
| `how-to-play/` | The How to Play page |
| `design.md` | Why the site exists, plus its palette, type and component rules |
| `video-work/clips.md` | The Runway prompts used for every clip. Raw takes and frames stay out of git. |

Each clip starts on the previous clip's last frame, so the walk has no cuts. To replace a clip, render the new one from the same start frame (see `clips.md`), then re-encode it for scrubbing:

```bash
ffmpeg -i raw.mp4 -map_metadata -1 -an -vf "unsharp=5:5:0.8:5:5:0.0" \
  -c:v libx264 -preset slow -crf 20 -pix_fmt yuv420p \
  -g 8 -keyint_min 8 -sc_threshold 0 -movflags +faststart video/<stop>.mp4
```

## Hosting

The site deploys as a free **Static Site** on Render:
- **Build Command:** leave empty
- **Publish Directory:** `.`
