# SalonBook: kivabe run korbe (Local + Deploy)

Duita folder: `salonbook-client` (Next.js + Better Auth) ar `salonbook-server` (Express API).
Duita alada GitHub repo banate hobe.

## 1. Agey ja lagbe
- Node.js 18+ (nodejs.org)
- MongoDB Atlas free cluster -> connection string
- Stripe account (test mode) -> Secret key
- ImgBB account -> API key (api.imgbb.com)
- Google Cloud Console -> OAuth Client ID/Secret
  - Authorized redirect URI: `http://localhost:3000/api/auth/callback/google`
  - Live e: `https://<your-client-domain>/api/auth/callback/google`

## 2. Server run
```bash
cd salonbook-server
npm install
cp .env.example .env      # Windows: copy .env.example .env
```
`.env` fill koro:
```
MONGODB_URI=mongodb+srv://...
MONGODB_DB=salonbook
CLIENT_URL=http://localhost:3000
STRIPE_SECRET_KEY=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...   (step 5 e pabe)
IMGBB_API_KEY=...
```
```bash
npm run dev     # http://localhost:5000
npm run seed    # sample services (optional)
```

## 3. Client run
```bash
cd salonbook-client
npm install
cp .env.example .env.local
```
`.env.local`:
```
MONGODB_URI=<server er motoi same>
MONGODB_DB=salonbook
BETTER_AUTH_SECRET=<openssl rand -base64 32 dile pabe, ba ekta lomba random string>
BETTER_AUTH_URL=http://localhost:3000
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
NEXT_PUBLIC_API_URL=http://localhost:5000/api
```
```bash
npm run dev     # http://localhost:3000
```

## 4. Admin account banano (public sign up e admin hoy na)
1. Website e normal Sign Up koro (jemon admin@salonbook.com)
2. Server folder e:
```bash
npm run make-admin -- admin@salonbook.com
```
3. Logout/login koro -> admin dashboard pabe.

## 5. Stripe webhook (local)
Stripe CLI install kore:
```bash
stripe login
stripe listen --forward-to localhost:5000/api/payments/webhook
```
Je `whsec_...` dekhabe seta server `.env` e `STRIPE_WEBHOOK_SECRET` e bosao, server restart koro.
Test card: `4242 4242 4242 4242`, je kono future date, je kono CVC.

## 6. Test flow
Customer account e service e jao -> Book -> Admin e confirm -> Customer My Appointments e Pay Now -> Stripe -> Success page (webhook ashle Paid hobe).

## 7. Deploy (Vercel, duita alada project)
**Server:** repo import -> Root = server folder. Env variables sob dao. `CLIENT_URL` = client er live URL (trailing slash chara).
**Client:** repo import. Env: sob dao, `BETTER_AUTH_URL` = client live URL, `NEXT_PUBLIC_API_URL` = `https://<server-domain>/api`.
**Stripe live webhook:** Stripe Dashboard -> Developers -> Webhooks -> endpoint `https://<server-domain>/api/payments/webhook`, events: `checkout.session.completed`, `checkout.session.expired`, `checkout.session.async_payment_succeeded`, `checkout.session.async_payment_failed`. Ei endpoint er signing secret ta server env `STRIPE_WEBHOOK_SECRET` e dao, redeploy.
**Google:** redirect URI te live URL add koro.

## 8. Notes
- Currency default USD (`STRIPE_CURRENCY`).
- Seed er service image gulo placeholder; admin dashboard theke upload kore replace koro.
- Home banner image Unsplash URL; chaile `src/app/page.tsx` e nijer image dao.
- Client ar server e `.env` kokhono GitHub e push korba na (.gitignore ache).
- Git commit: client e 20+, server e 12+ meaningful commit korte bhulo na.
