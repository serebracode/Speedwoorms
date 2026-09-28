# Speedwoorms

A minimal mobile **network worm-racing game**.

The visual language is deliberately reduced to a black field, sparse white background points and simple coloured worms. The client is published on GitHub Pages as a static Canvas app with no build step or dependencies; the actual game requires a real-time room server.

## Current client flow

The published client currently starts directly in **test mode**. Lobby and room UI remain in the source but are bypassed so worm geometry, animation and touch control can be tested immediately on a phone. The steps below describe the retained future network flow:

1. The player enters a name and selects a worm colour.
2. The player confirms **READY**.
3. The client enters a waiting-room screen.
4. The room server waits until every joined player is ready, then sends one shared countdown: **3 · 2 · 1 · GO**.
5. When the server declares the race complete, the client shows its authoritative `SCORE` table: place and name.

There is deliberately no local race, test countdown or fabricated local result. The published Pages build currently contains the visual client and room UI; until a room server is connected, it cannot create a shared room or start a race.

## Core movement

The worm has a fixed body length. It never stretches or shrinks.

The supplied motion is defined by seven keyframes:

- the first three frames unfold the body from the left;
- the fourth frame is a fully extended 637 px line;
- the last three frames pull the rear end forward and rebuild the folded form on the right.

The reference artboard is an iPhone 15 Pro portrait viewport (`393 × 852`). The game scene itself is authored as `852 × 393`, rotated 90° inside the portrait viewport and scaled uniformly on other screens. Geometry must never be stretched independently by axis.

The movement loop is fixed and cannot be gained through random tapping:

```
folded  _/\\__     →  extended  _____
right thumb →      →  left thumb →
```

- A rightward stroke with the **right thumb** unfolds the worm.
- The player releases that thumb.
- A rightward stroke with the **left thumb** folds the worm again.
- The player releases and repeats.

A leftward motion only reloads the same thumb for its next rightward working stroke. The client rejects a second working stroke while another finger is still down, and it rejects the wrong order. Fast, clean alternation produces more progress.

The seven supplied phases also define the worm's movement across the viewport. Completing the cycle advances the body by 253 reference pixels.

## Geometry and visual rules

- Portrait mobile viewport with an internally rotated `852 × 393` scene.
- The player worm is one solid `63.14 px` line with round ends and joins. Its centre-line is traced from the seven supplied reference phases; it is not a rectangle or a procedural sine arch.
- An opponent is a hollow outline in the layer behind the player.
- Supplied phase coordinates are treated as the source of truth for shape and placement; no procedural arch is substituted.
- Background points are sparse, white, randomly distributed across the whole viewport and drift slightly as progress increases.
- Race progress is a single thin line spanning the full viewport width. One race length equals one viewport width.

## Room rules

- Telegram supplies the default display name, which remains editable.
- Colour is the only initial worm customisation. Different forms are intentionally deferred to keep movement immediately readable.
- Every player presses **READY**.
- The server starts only when all joined players are ready, then every device receives one shared `GO` time.
- The room closes to new racers once the countdown begins.
- The server sends the final standings. Clients render them without inventing places.

## Required multiplayer backend

Telegram Mini Apps provide the launch surface, player identity and invite links. A separate backend is mandatory for real-time rooms; GitHub Pages can host the client but cannot host a persistent WebSocket room service.

The server owns:

- room IDs, participant list and ready state;
- Telegram `initData` validation and player identity;
- the authoritative start timestamp;
- each player's progress and current fold phase;
- finish order, disconnects and reconnect handling.

Clients send compact movement events, not Canvas geometry. WebSockets with client-side interpolation are sufficient for 1–10 players.

For a ten-player race, the player sees their own solid worm, a few nearest opponents as outlines, and the rest in a compact position list. Rendering every competitor directly behind the player would become a pink traffic accident.

## Telegram orientation

The app opens in portrait, draws the complete horizontal `852 × 393` game once at 90° across the portrait viewport, and calls Telegram Mini Apps' `lockOrientation()` when portrait is active. Touch coordinates are converted into the rotated game's coordinate system, so left/right thumbs and strokes remain correct when the phone is held for the horizontal game.

## Next implementation steps

1. Deploy the room server and define a WebSocket endpoint.
2. Connect the lobby to room creation/joining, real ready states and Telegram identity validation.
3. Send server-timed countdown, validated movement events and remote-worm state.
4. Send server-provided finish order and rematch handling.
5. Tune stroke threshold, rhythm tolerance and distance per valid cycle on real phones.
