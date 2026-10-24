# Le XI du Peuple — Olympique de Marseille fan vote

Fans vote in random head-to-head duels between OM players (2000–01 to today). The community's results
build a live 4-4-2 "top XI" on the pitch at the top of the page.

| Path | What it is |
|---|---|
| `index.html` | The public website: sign in with **Google**, votes stored in Firebase. Deployed to GitHub Pages by a workflow. |
| `firebase-config.js` | Firebase web keys (empty = demo mode, votes stay on the device). |
| `firestore.rules` | Database security rules (each fan can only edit their own votes, with anti-spam limits). |
| `artifact/index.html` | The claude.ai Artifact version (claude.ai accounts only). |

## Going live with Google sign-in (about 10 minutes, free)

### 1. Create the Firebase project
1. Go to https://console.firebase.google.com → **Add project** (e.g. `top-xi-om`). Analytics can be off.
2. Left sidebar: **Databases & Storage → Firestore Database → Create database** (production mode, region `eur3` / Europe).
3. Open the **Rules** tab, paste the contents of `firestore.rules`, click **Publish**.
4. **Project settings (gear) → Your apps → Web (`</>`)** → register the app → copy the `firebaseConfig`
   values into `firebase-config.js`. These keys are meant to be public; the rules protect the data.

### 2. Turn on Google sign-in
Left sidebar: **Security → Authentication → Get started → Sign-in method → Google → Enable** → pick a support email → Save.
(Can't find it? Type "Authentication" in the console's search bar, or open
`https://console.firebase.google.com/project/YOUR-PROJECT-ID/authentication/providers`.)

### 3. Publish the website on GitHub Pages
GitHub repo → **Settings → Pages → Build and deployment → Source: GitHub Actions**.
Every push to the branch then deploys the site to **https://pasteko72.github.io/Top-XI-OM/**
(workflow `.github/workflows/pages.yml`; it can also be run by hand from the Actions tab).

### 4. Allow the site's address to sign people in
Firebase → **Authentication → Settings → Authorized domains → Add domain** → `pasteko72.github.io`.

### 5. Block bots with App Check (reCAPTCHA Enterprise / Fraud Defense)
1. Google Cloud console, project `top-xi-om` → **Security → Fraud Defense** (reCAPTCHA) → enable the API if asked →
   **Create key**: platform **Website**, domain `pasteko72.github.io`, *no* checkbox challenge (score-based) → copy the **key ID**.
2. Firebase → **Security → App Check → Apps** → your web app → **reCAPTCHA Enterprise** → paste the key ID → Save.
3. Put the key ID in `firebase-config.js` (`RECAPTCHA_SITE_KEY`) and push.
4. Wait a day, then check **App Check → APIs → Cloud Firestore**: almost all requests should be "Verified".
   Then click **Enforce**. From then on, only the real site can read/write votes.

## How the vote works
- Each fan has one document `votes/<uid>` = `{ t: { playerId: [wins, losses] }, n, at }`.
- Everyone's documents are summed live in the browser. Each fan's weight per player is capped at 200 duels.
- Ranking = smoothed win rate `(wins + 3) / (duels + 6)`, so one lucky vote can't top a position.
- Duels are always between two players of the same position, and less-voted players come up more often.
- Fans can vote before signing in; those votes are added to their account when they sign in.

Player list: edit the `RAW` array in `index.html` (`[name, seasons at OM, position]`).
