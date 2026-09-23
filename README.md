# Speedwoorms

A minimal mobile worm-racing game prototype.

The visual language is deliberately reduced to a black field, sparse white background points and simple coloured worms. The current build lives on GitHub Pages as a static Canvas app with no build step or dependencies.

## Current flow

1. The player enters a name and selects a worm colour.
2. The player confirms **READY**.
3. The local prototype shows a room screen and can launch a test countdown: **3 · 2 · 1 · GO**.
4. In a networked room, every joined player must be ready before the host starts the same server-timed countdown.
5. When the distance line is complete, a `SCORE` table shows place and player name. The local build contains the single local result; the server will supply the full finish order in shared races.

The Pages build is a local interaction prototype. It does not yet create a shared room or connect real players.

## Core movement

The worm has a fixed body length. It never stretches or shrinks.

There are two resting states:

- **Folded** — a raised arch with both ends still touching the baseline.
- **Extended** — the same body lying as one continuous horizontal line.

The movement loop is fixed and cannot be gained through random tapping:

```
folded  _/\__     →  extended  _____
right thumb →      →  left thumb →
```

- A rightward stroke with the **right thumb** unfolds the worm.
- The player releases that thumb.
- A rightward stroke with the **left thumb** folds the worm again.
- The player releases and repeats.

A leftward motion only reloads the same thumb for its next rightward working stroke. The app rejects a second working stroke while another finger is still down, and it rejects the wrong order. Fast, clean alternation produces more progress.

The worm stays centered in the viewport. Movement is read through the slow inverse drift of background points rather than dragging the body across the screen.

## Geometry and visual rules

- Portrait mobile layout; no device rotation in the current Pages build.
- The player worm is a fully solid, rounded stroke.
- The opponent is a hollow outline in the layer behind the player.
- Both worms sit on one baseline.
- The folded arch is asymmetric: the short front segment is on the left; the longer rear segment is on the right.
- Body length is preserved mathematically: its central arc has constant length while its chord and height change.
- Background points are sparse, white, randomly distributed across the whole viewport and drift slightly as progress increases.
- Race progress is a single thin line spanning the full viewport width. The pink section represents completed distance. One race length equals one viewport width.

## Lobby rules

For the future shared mode:

- Telegram supplies the default display name, which remains editable.
- Colour is the only initial worm customisation. Different forms are intentionally deferred to keep movement immediately readable.
- Every player presses **READY**.
- The host starts only when the room is ready, then all devices receive one shared `GO` time.
- The room closes to new racers once the countdown begins.

## Multiplayer plan

Telegram Mini Apps can provide the launch surface, player identity and invite links. A separate backend is still required for real-time rooms.

The server should own:

- room IDs, participant list and ready state;
- the authoritative start timestamp;
- each player's progress and current fold phase;
- finish order and reconnect handling.

Clients need to send only compact movement state, not Canvas geometry. WebSockets with client-side interpolation are enough for 1–10 players.

For a ten-player race, the player sees their own solid worm, a few nearest opponents as outlines, and the rest in a compact position list. Rendering every competitor directly behind the player would become a pink traffic accident.

## Telegram orientation

Telegram Mini Apps (Bot API 8+) provide `lockOrientation()`. It locks the current device orientation; it does not command a phone to rotate. The planned Mini App should start in portrait, then lock portrait only when it detects that portrait is already active.

## Next implementation steps

1. Tune stroke threshold, rhythm tolerance and distance per valid cycle on real phones.
2. Replace the local lobby preview with WebSocket rooms and Telegram identity validation.
3. Add host controls, real ready states and a server-timed countdown.
4. Add server-provided finish order, rematch and lightweight results.
