# Nawaz Sharif School of Eminence — Boys Campus Haroonabad — Website

Firebase is already connected (project: `nsse-boys-campus-hnd`). This
covers what's left to finish setup, plus what each new feature does.

## ✅ Already Done

- Firebase config is pasted into all 8 pages.
- Admin dashboard is **restricted to one email**: `learnwithhusnain0@gmail.com`.
  Any other account will be signed out automatically, even if it has a
  valid password.
- Contact info, the principal's message, and the homepage stats are now
  editable from the admin panel (Site Content tab) instead of being
  hardcoded in the HTML.
- Homepage stat numbers count up from 0 when scrolled into view.
- On the homepage, the large crest logo visually travels up into the
  header's logo spot as you scroll.

## 🔐 Step 1 — Create the Admin Account

Firebase Console → Authentication → **Users → Add user**:
- Email: `learnwithhusnain0@gmail.com`
- Password: choose a strong password

This is the **only** email the dashboard will accept. If you ever need a
second admin, open every `admin.html` and `admin.html`
file, find `ALLOWED_ADMIN_EMAIL`, and add the logic for multiple emails
(or ask me to add multi-admin support).

## 📜 Step 2 — Firestore Security Rules

Firebase Console → Firestore Database → **Rules** → paste:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    function isAdmin() {
      return request.auth != null &&
             request.auth.token.email == 'learnwithhusnain0@gmail.com';
    }

    match /announcements/{docId} {
      allow read: if true;
      allow write: if isAdmin();
    }

    match /messages/{docId} {
      allow create: if true;
      allow read, update, delete: if isAdmin();
    }

    match /siteContent/{docId} {
      allow read: if true;
      allow write: if isAdmin();
    }
  }
}
```

Click **Publish**. This enforces the email restriction on the server
side too — not just in the admin panel's JavaScript — so no one else
can write data even if they inspect the code.

Also enable Firestore in the console if you haven't already:
**Build → Firestore Database → Create database.**

## 🧩 What's Editable From the Admin Panel Now

Log in at `admin.html` → Dashboard has 4 tabs:

| Tab | Controls |
|---|---|
| **Announcements** | Add/edit/delete posts shown on the homepage ticker and the Announcements page |
| **Messages** | Read messages submitted through the Contact page form |
| **Site Content** | Phone, email, address, office hours, footer about text, principal's quote/name/role, and all 4 homepage stat values + labels |
| **Settings** | See logged-in email, send yourself a password reset |

Anything in **Site Content** left blank keeps the website's built-in
default text — you don't have to fill in every field right away.

## 🌐 Step 3 — Go Live (Hosting)

```bash
npm install -g firebase-tools
firebase login
cd nsse-site
firebase init hosting
# Public directory: . (current folder)
# Single-page app: No
firebase deploy
```

You'll get a live URL like `https://nsse-boys-campus-hnd.web.app`.

You can also upload this folder as-is to any static host (Netlify,
Vercel, cPanel) — no build step required.

## 🖼️ Principal's Photo

Add a photo file named `principal.jpg` inside `assets/images/`. Until
then, the homepage shows a placeholder initial in the photo frame.

## 📁 File Structure

Each page is a single self-contained `.html` file — its CSS is in a
`<style>` block and its JS is in `<script>` blocks, inside that same
file. Nothing is shared externally except Google Fonts and the Firebase
SDK (both loaded from CDN).

```
nsse-site/
├── index.html            Home — hero, features, principal message, live stats, announcements ticker
├── academics.html
├── admissions.html
├── announcements.html    Full announcements list (live from Firestore)
├── faculty.html
├── contact.html          Contact form → Firestore "messages"
├── admin/
│   ├── index.html        Admin login (restricted to one email)
│   └── dashboard.html    Announcements + Messages + Site Content + Settings
└── assets/images/logo.png
```

There's no separate "About" page — the about blurb lives in the footer
of every page, and is editable from Site Content.
