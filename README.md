# RPT Purchase Order Generator

Single-file, mobile-friendly web app to create, save, edit, duplicate, and
export Purchase Orders for **PT. Redimas Padangan Teknik**.

Everything lives in [`index.html`](index.html) (logo embedded as base64, no build
step). Storage + login use Firebase.

## Features

- 🔒 Password login (Firebase Auth)
- 🧾 PO editor: recipient, PO meta, dynamic item rows, notes, delivery, misc fee
- 🧮 Auto totals: line totals, subtotal, discount, **PPN 11%**, grand total
- 💾 Save projects to the cloud → reopen, edit, or **duplicate** later
- 📄 Export PDF named `PO - <PO Number> - Redimas Padangan Teknik.pdf`
- 📱 Responsive (phone + desktop), fixed RPT company header + Director signature

## Run locally

Just open `index.html` in a browser, **or** serve it (recommended, needed for
Firebase auth domains):

```bash
npx serve .
# or
python -m http.server 8000
```

Without Firebase configured it runs in **LOCAL mode**: data is saved only in the
current browser and the login password is `rpt`. Good for trying it out.

## Connect Firebase (real login + shared cloud storage)

1. Create a project at <https://console.firebase.google.com>.
2. **Build → Authentication → Sign-in method →** enable **Email/Password**.
3. **Authentication → Users → Add user** — create one login per person
   (email + password). These are the only people who can open the app.
4. **Build → Firestore Database → Create database** (Production mode).
5. **Firestore → Rules** → paste the contents of [`firestore.rules`](firestore.rules) → **Publish**.
6. **Project settings (⚙️) → Your apps → Web app** → copy the `firebaseConfig`.
7. In `index.html`, find `var firebaseConfig = {` and replace the placeholder
   values with yours. Saving the file flips the app from LOCAL to Firebase mode
   automatically.
8. **Authentication → Settings → Authorized domains** → add your hosting domain
   (e.g. `raniaarn.github.io`) and `localhost`.

All signed-in users share the same POs (company-wide access).

## Deploy (free) — GitHub Pages

```bash
git remote set-url origin https://github.com/raniaarn/po-rpt.git   # your repo
git add -A && git commit -m "Deploy PO app"
git push -u origin main
```

Then on GitHub: **Settings → Pages → Build from branch → `main` / root**.
App goes live at `https://raniaarn.github.io/po-rpt/`. Remember to add that
domain to Firebase Authorized domains (step 8).

## Calculation

```
line total = qty × unit price − line discount
subtotal   = Σ(qty × unit price)
discount   = Σ line discounts
PPN (11%)  = 0.11 × (subtotal − discount)
total      = subtotal − discount + PPN + misc fee
```

## Files

| File | Purpose |
|------|---------|
| `index.html` | The whole app (UI + logic + embedded logo) |
| `firestore.rules` | Security rules to paste into Firebase |
| `logo-with-text_hk2ofr.webp` | Source logo (already embedded in `index.html`) |
| `docs/superpowers/specs/` | Design spec |
