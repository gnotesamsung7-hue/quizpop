# TUMPAK! Ganern

A Pinoy live quiz game: the host shows a PIN and QR code on a big screen, players join on their phones, and fast correct answers score the most points.

## One-time setup (about 10 minutes)

1. Go to https://console.firebase.google.com and create a project (you can turn off Google Analytics).
2. **Build → Realtime Database → Create database.** Pick a location (Singapore, asia-southeast1, is closest to the Philippines) and start in **locked mode**.
3. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable → Save.**
4. **Realtime Database → Rules.** Replace everything with the rules below, then click **Publish**.
5. **Project settings (gear icon) → General → Your apps → Web (`</>`).** Register an app (no Firebase Hosting needed). Copy the values from the `firebaseConfig` block into the top of `index.html`, replacing each `PASTE_...` value. Make sure `databaseURL` is filled in.
6. Upload `index.html` to a GitHub repo and turn on **Settings → Pages → Deploy from a branch → main / root**.

## Database rules

```json
{
  "rules": {
    "games": {
      "$pin": {
        ".read": true,
        ".write": "auth != null && data.child('host').val() === auth.uid",
        "host": {
          ".write": "auth != null && !data.exists() && newData.val() === auth.uid"
        },
        "players": {
          "$uid": {
            ".write": "auth != null && auth.uid === $uid",
            ".validate": "newData.child('name').isString() && newData.child('name').val().length <= 24"
          }
        }
      }
    }
  }
}
```

## Limits

Firebase's free Spark plan allows 100 devices connected at the same time, so about 95 players plus the host. Quizzes are saved in the host's browser; use **Export** in the quiz editor to back them up.
