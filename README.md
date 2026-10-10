# Eventra — Firebase Event Management

A responsive event-management mini project built with Bootstrap 5, vanilla JavaScript modules, Firebase Authentication and Cloud Firestore.

## Features
- Student sign up and sign in; no admin sign-up screen.
- Unique email enforced by Firebase Authentication.
- Roll-number uniqueness reservation using a Firestore transaction.
- Student dashboard, event discovery, event registration and registration history.
- Admin dashboard: create, edit and delete events; list/search/remove student profiles.
- Form validation, capacity checks, animated cards, responsive dark glassmorphism UI.
- Firestore security rules included.

## 1. Firebase setup
1. Open https://console.firebase.google.com/ and select project `events-a670e`.
2. In **Authentication → Sign-in method**, enable **Email/Password**.
3. In **Firestore Database**, create a database.
4. In **Firestore → Rules**, paste the contents of `firestore.rules` and publish.
5. In **Project settings → Your apps**, check that `firebase-config.js` matches the web app config supplied for your project.
6. Run the site from a local web server; ES modules do not work reliably when opening `index.html` directly as a `file://` URL.

## 2. Create the admin manually (no admin signup)
1. Firebase Console → **Authentication → Users → Add user**. Enter the admin email and a strong password.
2. Copy the new user's **UID**.
3. Firestore → create collection `users` (if it does not exist) → add a document whose document ID is exactly that UID.
4. Add these fields:
   - `uid` (string): same UID
   - `name` (string): Admin
   - `email` (string): same email used in Authentication
   - `role` (string): `admin`
   - `rollNumber` (string): `ADMIN`
   - `department` (string): `Administration`
5. Sign in using that admin email/password. Admin privileges are validated by Firestore rules against the profile document, not a client-side admin switch.

## 3. Run locally
Use VS Code's **Live Server** extension or another static server. Example with Python installed:
```bash
python -m http.server 5500
```
Then open `http://localhost:5500` in your browser.

## Important notes
- Client-side duplicate checks are not enough by themselves; the roll-number claim transaction and Firestore rules are included to reduce races. Test the rules with Firebase Rules Playground before deploying.
- For production, consider Cloud Functions to perform registration/capacity updates and admin student deletion, plus App Check and stricter schema validation.
- Removing a student from this UI removes their Firestore profile, roll claim and registrations. It does **not** delete/disable the Firebase Authentication account; disable/delete that account from Firebase Console or add a trusted Cloud Function for full removal.
- Firebase web config values are public identifiers and are normally shipped in browser code. Security depends on Authentication, Firestore Rules, and backend controls—not hiding the config. Do not put service-account credentials in this project.
- The event page requires login to read events. If you want public event browsing, adjust the rules intentionally.
