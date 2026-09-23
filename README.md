# Speedwoorms

A tiny touch prototype for a two-thumb worm-racing game.

## Play

Open the GitHub Pages URL on a phone in landscape orientation.

1. Put one thumb on each side of the screen.
2. Move both thumbs inward to contract the worm.
3. Release to make it crawl forward.

The current version is deliberately local-only. It tests the one thing that matters before multiplayer: whether moving the worm feels good.

## Technical shape

Static HTML, CSS and Canvas. No build step and no dependencies, so GitHub Pages can publish it directly.

Next steps: tune the crawl feel, add an opponent silhouette, then build invited two-player rooms through Telegram Mini Apps and WebSockets.
