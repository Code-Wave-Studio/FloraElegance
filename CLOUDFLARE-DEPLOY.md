# FloraElegance — Cloudflare Pages Frontend Deploy

**Website URL:** `https://floraelegance.codewavestudio.space`  
**API (Hostinger):** `https://api.floraelegance.codewavestudio.space/api`  
This guide covers **only** the React frontend on Cloudflare Pages.  
Database + PHP API stay on Hostinger (separate deploy).

---

## Architecture

```
User browser
    │
    ▼
Cloudflare Pages  ──API calls──►  Hostinger (api.floraelegance.codewavestudio.space)
floraelegance.codewavestudio.space
```

---

## 1. Prerequisites

- Cloudflare account (free plan works)
- GitHub/GitLab repo with this project (recommended), **or** manual upload
- Hostinger API already live at `https://api.floraelegance.codewavestudio.space/api`
- DNS access for `codewavestudio.space`

---

## 2. Cloudflare Pages — new project

### Option A — Connect Git (recommended)

1. [Cloudflare Dashboard](https://dash.cloudflare.com) → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**
2. Select your repository
3. Build settings:

| Setting | Value |
|---------|-------|
| **Production branch** | `main` (or your branch) |
| **Root directory** | `/` (repo root — this folder) |
| **Build command** | `npm run build` |
| **Build output directory** | `dist` |
| **Framework preset** | Vite |

4. **Environment variables** (Production):

| Variable | Value |
|----------|-------|
| `VITE_API_BASE_URL` | `https://api.floraelegance.codewavestudio.space/api` |
| `VITE_RAZORPAY_KEY_ID` | `rzp_live_xxxxxxxx` (or test key) |
| `VITE_BASE_PATH` | `/` |

5. Click **Save and Deploy**

### Option B — Direct upload (no Git)

```powershell
cd C:\xampp\htdocs\FloraElegance\FloraElegance\plant\frontend
npm install
npm run build
```

Then Cloudflare → Pages → **Upload assets** → upload the `dist/` folder.

> Set env vars before build if uploading manually (`.env.production` is already configured).

---

## 3. Custom domain

1. Pages project → **Custom domains** → **Set up a custom domain**
2. Enter: `floraelegance.codewavestudio.space`
3. Cloudflare will add the required DNS record automatically (if domain is on Cloudflare)

### DNS (if setting manually)

| Type | Name | Value |
|------|------|-------|
| CNAME | `floraelegance` | `<project-name>.pages.dev` |

Enable **Proxied** (orange cloud) for CDN + SSL.

---

## 4. SPA routing (already configured)

File `public/_redirects` is included in the build:

```
/*    /index.html   200
```

This ensures `/admin`, `/shop`, `/product/1` etc. work on refresh.

---

## 5. Test the live site

| Page | URL |
|------|-----|
| Home | `https://floraelegance.codewavestudio.space/` |
| Shop | `https://floraelegance.codewavestudio.space/shop` |
| Admin | `https://floraelegance.codewavestudio.space/admin` |
| Auth | `https://floraelegance.codewavestudio.space/auth` |

**Admin login:** `admin@ugaoo.com` / `admin123`

Open browser DevTools → Network tab → confirm API calls go to  
`api.floraelegance.codewavestudio.space` (not localhost).

---

## 6. Environment variables reference

Copy from `.env.production.example`:

```env
VITE_BASE_PATH=/
VITE_API_BASE_URL=https://api.floraelegance.codewavestudio.space/api
VITE_RAZORPAY_KEY_ID=rzp_test_w3hP8z2jN1245A
```

> Cloudflare Pages rebuilds on every push. Local `.env.production` is used when you build manually.

---

## 7. Updating the frontend

### With Git connected
Push changes to this repo → Cloudflare auto-rebuilds.

### Manual
```powershell
npm run build
```
Re-upload `dist/` or use `wrangler pages deploy dist`.

---

## 8. Common problems

| Problem | Solution |
|---------|----------|
| Blank page | Check build logs; confirm `dist/` has `index.html` + `assets/` |
| API calls fail (CORS) | Add Cloudflare URL to `ALLOWED_ORIGINS` in Hostinger `backend/.env` |
| API calls go to localhost | Set `VITE_API_BASE_URL` in Cloudflare Pages env vars; redeploy |
| 404 on `/admin` refresh | Confirm `_redirects` exists in `dist/` after build |
| Razorpay not loading | Use HTTPS; set live `VITE_RAZORPAY_KEY_ID` |
| Images not showing | Images are served from Hostinger `uploads/` — API must be live |

---

## 9. Cloudflare Pages preview URLs

Each deploy gets a preview URL like `abc123.floraelegance.pages.dev`.  
Add preview domain to Hostinger `ALLOWED_ORIGINS` if you test previews:

```env
ALLOWED_ORIGINS=https://floraelegance.codewavestudio.space,https://floraelegance.pages.dev,http://localhost:5173
```

---

## Quick checklist

- [ ] Hostinger API live and tested
- [ ] Cloudflare Pages project created
- [ ] Build settings: root `/`, output `dist`
- [ ] Env vars: `VITE_API_BASE_URL`, `VITE_RAZORPAY_KEY_ID`
- [ ] Custom domain `floraelegance.codewavestudio.space` connected
- [ ] Site loads, shop works, admin login works
- [ ] No CORS errors in browser console





floraelegance-frontend/          ← GitHub repo name (example)
│
├── .env.production              ← Production API URL (Cloudflare build use karega)
├── .env.production.example      ← Template (optional)
├── .gitignore                   ← Zaroori (neeche diya hai)
├── CLOUDFLARE-DEPLOY.md         ← Deploy guide
├── eslint.config.js             ← Lint config
├── index.html                   ← Main HTML entry
├── package.json                 ← Dependencies + build scripts
├── package-lock.json            ← Lock file (commit karo)
├── vite.config.js               ← Vite build config
├── README.md                    ← Project info
│
├── public/                      ← Static files (build mein copy hote hain)
│   ├── .htaccess                ← (Hostinger ke liye tha; Cloudflare ignore karta hai)
│   ├── _redirects               ← ★ SPA routing — /admin, /shop refresh fix
│   ├── favicon.svg
│   └── icons.svg
│
└── src/                         ← React source code
    ├── main.jsx                 ← App entry point
    ├── App.jsx                  ← Routes + layout
    ├── App.css
    ├── index.css                ← Global + Tailwind styles
    ├── config.js                ← API URL + Razorpay key (env se)
    │
    ├── context/
    │   ├── AuthContext.jsx      ← Login / logout / session
    │   └── CartContext.jsx      ← Cart state
    │
    ├── components/
    │   ├── Navbar.jsx
    │   ├── Footer.jsx
    │   ├── ProtectedRoute.jsx   ← Login required routes
    │   ├── ScrollToTopButton.jsx
    │   └── admin/
    │       ├── AdminCouponsTab.jsx
    │       ├── AdminCustomersTab.jsx
    │       ├── AdminInventoryTab.jsx
    │       └── AdminThemeToggle.jsx
    │
    ├── pages/
    │   ├── Home.jsx
    │   ├── ProductListing.jsx   ← /shop
    │   ├── ProductDetails.jsx   ← /product/:id
    │   ├── Categories.jsx
    │   ├── Cart.jsx
    │   ├── Checkout.jsx
    │   ├── Auth.jsx             ← Login / signup
    │   ├── AdminDashboard.jsx   ← /admin
    │   ├── CustomerDashboard.jsx← /dashboard
    │   ├── Wishlist.jsx
    │   ├── TrackOrder.jsx
    │   ├── Contact.jsx
    │   ├── FAQs.jsx
    │   ├── PrivacyPolicy.jsx
    │   ├── TermsConditions.jsx
    │   └── ShippingPolicy.jsx
    │
    ├── utils/
    │   ├── commerceHelpers.js   ← Shipping, coupons calc
    │   ├── invoiceGenerator.js  ← PDF invoice
    │   └── orderHelpers.js
    │
    ├── styles/
    │   └── admin-dark.css       ← Admin panel dark theme
    │
    └── assets/
        ├── hero.png
        ├── react.svg
        └── vite.svg