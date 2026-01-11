# Purpose
To rejoin development on two divergent branches with features.

# History
The file `fps_game-12.html` was the source for two valuable lines of development:
- `fps_game_day_night.html`
- `fps_game-13.html`

# Goal
Take the logical changes between
- `fps_game-12.html` and `fps_game_day_night.html`
- `fps_game-12.html` and `fps_game-13.html`
and apply them to a unified version that will be called `fps_game_unified.html`

# Tool to assist
This command line will help you to identify where the two versions are identical:
```
samesame -v fps_game_day_night.html fps_game-13.html
```
The places to merge will be outside these areas.
