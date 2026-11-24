# Chess

A light chess game that works offline and installs like an app (PWA). One HTML file, no build step, no dependencies: open `index.html` and play.

## Play modes

- **Vs computer** with three difficulty levels
- **Friend on the same Wi-Fi** for local network play
- **Play online** through a Firebase lobby: create a room (optional password), join an open room, or play by link
- **Puzzles** to solve
- **Game review** with move-by-move history

## Features

- Time controls: 3+2, 5+3, 10+0, 15+10
- Hint, undo, board flip, sound, and board themes
- English and Arabic interface
- Game history saved locally in your browser (localStorage)
- Installable and works offline after the first visit (service worker)

## Run it

Open `index.html` in any modern browser, or host the folder on any static file server such as GitHub Pages.

## Install and offline

The game is a Progressive Web App. Open it once over the internet (for example on GitHub Pages) and the service worker caches it. After that it opens offline, and you can install it from your browser menu ("Install app" or "Add to Home Screen"). Vs computer, puzzles, review and history all work offline. Online lobby play needs internet.

Files: `sw.js` (service worker), `manifest.webmanifest`, icon PNGs. The cache is versioned: **when you change `index.html`, bump `CACHE_VERSION` in `sw.js`**. Players then see a "New version ready" bar and tap Update. The service worker only works over `https://` or `localhost`, not from a `file://` page.

## Online play: how security works

The online lobby uses Firebase Realtime Database with **no sign-in**. Be clear about what that means:

- A room password is checked **in the browser**. The password hash is stored in the room so the joining browser can compare it. Anyone who can read the lobby can read that hash and try to guess the password offline. The game uses PBKDF2 (150,000 rounds), which makes guessing slow, but this is a "keep casual people out" lock, not real access control.
- Move data lives under the room id. Anyone who knows or can guess a room id could read it or add moves. Room ids are random, but they are listed in the open lobby.
- Do not treat online games as private or put anything sensitive in the player name.

To really enforce who can read and write, the app would need Firebase Authentication (for example anonymous sign-in) plus rules tied to the signed-in user. That is not built in yet.

### Firebase rules (do this once)

`database.rules.json` has the rules for this app. They do not add logins, but they close the obvious holes: nothing outside `lobby` and `games` can be read or written, the game list cannot be browsed, the shape and size of every record is checked, game events are append-only (no editing or deleting someone's moves), and only whole-room deletion is allowed for cleanup.

1. Open the Firebase Console, project `chess-17413`.
2. Go to Build, Realtime Database, then the **Rules** tab.
3. Paste the contents of `database.rules.json` and press **Publish**.
4. Test: open the lobby, create a room, join it from a second device, play a few moves, resign.

Pasting the file here does nothing by itself: Firebase only enforces rules published in the console (or deployed with the Firebase CLI).

## License

Copyright (c) 2026 Fares Mahmoud (faresdief). All rights reserved. See `LICENSE`.
