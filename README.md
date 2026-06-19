# FloraElegance — Frontend (Cloudflare Pages)

React + Vite storefront for **FloraElegance** plant e-commerce.

| | URL |
|---|-----|
| **Live site** | `https://floraelegance.codewavestudio.space` |
| **API (Hostinger)** | `https://api.floraelegance.codewavestudio.space/api` |

Backend + database are hosted separately on Hostinger. This repo is **frontend only**.

---

## Quick start (local)

```bash
npm install
cp .env.example .env
npm run dev
```

Open `http://localhost:5173`

---

## Production build

```bash
npm install
npm run build
```

Output: `dist/` (Cloudflare builds this automatically on deploy)

---

## Deploy to Cloudflare Pages

1. Push this folder to GitHub as a **new repository**
2. Cloudflare → **Workers & Pages** → **Create** → **Connect to Git**
3. Settings:

| Setting | Value |
|---------|-------|
| Build command | `npm run build` |
| Build output | `dist` |
| Root directory | `/` |

4. **Environment variables** (Production):

| Variable | Value |
|----------|-------|
| `VITE_API_BASE_URL` | `https://api.floraelegance.codewavestudio.space/api` |
| `VITE_RAZORPAY_KEY_ID` | your Razorpay key |
| `VITE_BASE_PATH` | `/` |

5. Custom domain: `floraelegance.codewavestudio.space`

Full guide: [CLOUDFLARE-DEPLOY.md](./CLOUDFLARE-DEPLOY.md)

---

## Demo logins (after API + DB are live)

| Role | Email | Password |
|------|-------|----------|
| Admin | `admin@ugaoo.com` | `admin123` |
| Customer | `jane@example.com` | `admin123` |

---

## Tech stack

- React 19 + Vite 8
- Tailwind CSS 4
- React Router 7
- Axios → Hostinger PHP API

---

**Designed & developed by [CodeWave Studio](https://www.codewavestudio.space/)**
