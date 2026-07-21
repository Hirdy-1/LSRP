# Firebase setup for Los Santos Roleplay

This site uses Firebase for two things:
- **Firestore** — stores Staff Applications and Business Applications submitted on the Apply page.
- **Authentication** — lets staff log in at `login.html` to view/manage submissions on `staff.html`.

## 1. Create a Firebase project
1. Go to https://console.firebase.google.com
2. Click **Add project**, name it whatever you like, finish the wizard.

## 2. Register a Web App
1. In your project, click the **Web (`</>`)** icon to add a web app.
2. Copy the `firebaseConfig` object it gives you.
3. Paste those values into `firebase-config.js` in this folder, replacing the placeholders.

## 3. Turn on Email/Password sign-in
1. In the console: **Build -> Authentication -> Sign-in method**.
2. Enable **Email/Password**.

## 4. Create your staff accounts
1. **Build -> Authentication -> Users -> Add user**.
2. Add an email + password for each staff member who needs dashboard access.
3. There's no public sign-up page — this is the only way accounts get created, on purpose.

## 5. Create the Firestore database
1. **Build -> Firestore Database -> Create database**.
2. Start in **production mode**.
3. Pick any region.

## 6. Set Firestore security rules
Go to **Firestore Database -> Rules** and paste this in:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /staffApplications/{docId} {
      allow create: if true;
      allow read, update, delete: if request.auth != null;
    }
    match /businessApplications/{docId} {
      allow create: if true;
      allow read, update, delete: if request.auth != null;
    }
  }
}
```

This means: anyone can submit an application (`create`), but only a signed-in staff account can view, update the status, or delete applications (`read`, `update`, `delete`).

## 7. Test it
1. Open `apply.html`, submit both forms.
2. Open `login.html`, sign in with a staff account you created in step 4.
3. You should land on `staff.html` and see both applications listed with a status dropdown (Pending / Approved / Denied).

## Notes
- These pages use the Firebase **compat** SDK loaded from a CDN — no build step or npm install required.
- If you ever want a public sign-up flow for staff instead of manually adding users, that's a separate change — ask and it can be added.
