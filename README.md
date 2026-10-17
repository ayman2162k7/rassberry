# Rassberry 🍓 — realtime chat (Firebase + Vercel)

Static site, no build step. Google sign-in, Firestore realtime database, WebRTC calls.

## 1. Firebase (free Spark plan)
1. https://console.firebase.google.com → Add project.
2. Build → **Authentication** → Get started → Sign-in method → enable **Google**.
3. Build → **Firestore Database** → Create database (production mode, nearest region).
4. Firestore → **Rules** → paste the contents of `firestore.rules` → Publish.
5. Project settings → Your apps → Web (`</>`) → register → copy the config into `firebase-config.js`.

## 2. Deploy on Vercel
1. Upload this folder to a GitHub repo → vercel.com → Add New Project → import it. Framework preset: **Other**, no build command, output directory blank. (Or run `npx vercel` inside this folder.)
2. Copy your live domain (e.g. `rassberry.vercel.app`).
3. Firebase → Authentication → Settings → **Authorized domains** → Add that domain. (Without this, Google sign-in fails with `auth/unauthorized-domain`.)

## 3. Use
Open the site, sign in, pick a unique username. Add friends by searching their username or sharing the invite link (`?ref=username`).

## Notes
- Images, voice messages and documents are stored inside Firestore documents (limit ~700 KB each) so everything stays on the free plan. Firebase Storage now needs a billing account; if you add one, move media there.
- Calls use a public STUN server. Behind strict networks/mobile carriers add a TURN server in `firebase-config.js` (`iceServers`).
- Security comes from `firestore.rules`: only chat members read a chat, only authors edit/delete their messages. Messages are not end-to-end encrypted (you, as project owner, can read the database).
- Free-tier limits: ~50k reads / 20k writes per day.

## v2 update
Replace `app.js`, `style.css` and `firestore.rules` with the new versions, then **republish the rules** in Firebase (Firestore > Rules > Publish) and redeploy. New users/prefs live in `users/{uid}/private`, so the new rules are required.
Privacy/blocking toggles are enforced by the app (client side); they hide data in the UI but are not server-enforced.
Not included yet: group chats and group calls, SMS/TOTP two-factor, large video upload (needs external storage such as Cloudinary).

## v3 UI update
Replace `app.js`, `style.css`, `index.html` and add the new `icons.js` (Lucide icons, ISC licence). `firestore.rules` is unchanged, so no need to republish rules.

## Upgrading the live v1 site without losing data
Deploy these files to the SAME Vercel project and keep the SAME Firebase project (`firebase-config.js` of v1 - do not overwrite it). Users sign in with the same Google account, so they keep their UID, friends and chats. Then publish `firestore.rules` in that Firebase project. To ship future updates, bump `APP_VERSION` in `app.js` and `version.json` together: open tabs show an "Update now" banner.
