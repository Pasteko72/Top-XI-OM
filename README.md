# Le XI du Peuple — Olympique de Marseille fan vote

A single-page app where fans vote in random head-to-head duels between OM players (2000–01 to today).
The community's results build a live 4-4-2 "top XI" on the pitch at the top of the page.

- `index.html` — the whole app (player list, pitch, duels, rankings).
- Votes are stored per fan (`votes/<fan id>`, one document holding that fan's win/loss tallies) in the
  claude.ai Artifact database and summed for everyone in the browser.
- Ranking = smoothed win rate `(wins + 3) / (duels + 6)`, so one lucky vote can't top a position.
- Duels are always between two players of the same position, picking less-voted players more often.

Player list: edit the `RAW` array in `index.html` (`[name, seasons at OM, position]`).
