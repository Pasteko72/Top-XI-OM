# Le XI du Peuple — Olympique de Marseille fan vote

Fans vote in random head-to-head duels between OM players (2000–01 to today). The community's results
build a live 4-4-2 "top XI" on the pitch at the top of the page.

| Path | What it is |
|---|---|
| `web/` | The public website: sign in with **Google or Facebook**, votes stored in Firebase. Deployed to GitHub Pages. |
| `web/firebase-config.js` | Your Firebase keys go here (empty = demo mode, votes stay on the device). |
| `firestore.rules` | Database security rules (each fan can only edit their own votes, with anti-spam limits). |
| `index.html` | The claude.ai Artifact version (claude.ai accounts only). |

## Going live with Google + Facebook sign-in (about 15 minutes, free)

### 1. Create the Firebase project
1. Go to https://console.firebase.google.com → **Add project** (e.g. `top-xi-om`). Analytics can be off.
2. **Build → Firestore Database → Create database** (production mode, region `eur3` / Europe).
3. Open the **Rules** tab, paste the contents of `firestore.rules`, click **Publish**.
4. **Project settings (gear) → Your apps → Web (`</>`)** → register the app → copy the `firebaseConfig`
   values into `web/firebase-config.js`. These keys are meant to be public; the rules protect the data.

### 2. Turn on Google sign-in
**Build → Authentication → Get started → Sign-in method → Google → Enable** → pick a support email → Save.

### 3. Turn on Facebook sign-in
1. https://developers.facebook.com/apps → **Create app** → use case *Authenticate and request data from users with Facebook Login*.
2. Copy the **App ID** and **App secret** (App settings → Basic).
3. In Firebase: **Authentication → Sign-in method → Facebook → Enable**, paste the App ID and secret,
   and copy the **OAuth redirect URI** Firebase shows you.
4. Back in Facebook: **Facebook Login → Settings → Valid OAuth Redirect URIs** → paste that URI → Save.
5. Fill in a Privacy Policy URL in App settings → Basic, then switch the app to **Live** so everyone (not just you) can log in.

### 4. Publish the website on GitHub Pages
1. GitHub repo → **Settings → Pages → Build and deployment → Source: GitHub Actions**.
2. Push any change to `web/` (or run the *Deploy site to GitHub Pages* workflow by hand from the **Actions** tab).
3. The site is at **https://pasteko72.github.io/Top-XI-OM/**.

### 5. Allow the site's address to sign people in
Firebase → **Authentication → Settings → Authorized domains → Add domain** → `pasteko72.github.io`.

## How the vote works
- Each fan has one document `votes/<uid>` = `{ t: { playerId: [wins, losses] }, n, at }`.
- Everyone's documents are summed live in the browser. Each fan's weight per player is capped at 200 duels.
- Ranking = smoothed win rate `(wins + 3) / (duels + 6)`, so one lucky vote can't top a position.
- Duels are always between two players of the same position, and less-voted players come up more often.
- Fans can vote before signing in; those votes are added to their account when they sign in.

Player list: edit the `RAW` array in `web/index.html` (`[name, seasons at OM, position]`).
