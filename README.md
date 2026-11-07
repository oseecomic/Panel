# Osee Panel Store

Next.js shop starter for selling Pterodactyl servers.

## Included
- Responsive dark storefront
- Editable sample plans in `lib/config.ts`
- Order creation stored in Supabase
- Server-side Pterodactyl Application API adapter
- Protected admin deploy endpoint
- Optional Midtrans notification signature verification endpoint

## Important deployment status
This is a functional starter, not a fully production-ready payment/admin system.
The storefront creates pending orders. It does **not** accept or verify QRIS payments yet.
Do not deploy paid automatic provisioning until you implement a trusted payment checkout flow,
an admin authentication interface, rate limiting, monitoring, and test the Pterodactyl
egg/location/allocation configuration.

The Midtrans webhook route verifies a notification signature and marks a matching pending
order as paid. It does not create a Snap transaction or QRIS checkout. Configure a real
Midtrans checkout integration before using that webhook in production.

## 1. Install
```bash
npm install
cp .env.example .env.local
npm run dev
```

## 2. Supabase
1. Create a Supabase project.
2. Open SQL Editor.
3. Run `supabase/schema.sql`.
4. Copy project URL and anon key to `.env.local`.
5. Copy service-role key to `SUPABASE_SERVICE_ROLE_KEY`. Never expose this key in frontend code.

## 3. Pterodactyl
Create an Application API key in the Pterodactyl admin panel and set:
- `PTERO_PANEL_URL`
- `PTERO_APPLICATION_API_KEY`
- `PTERO_DEFAULT_NEST_ID`
- `PTERO_DEFAULT_EGG_ID`
- `PTERO_DEFAULT_NEST_ID`
- `PTERO_DEFAULT_LOCATION_ID`
- `PTERO_DEFAULT_ALLOCATION_ID`
- `ADMIN_ACTION_SECRET`
- `PTERO_DEFAULT_LOCATION_ID`
- `PTERO_DEFAULT_ALLOCATION_ID`

The API key must be an Application API key with only the permissions required to find/create users
and create servers. Keep it server-side. Confirm that the selected egg, Docker image, startup command,
environment variables, location, and allocation are correct for the game you sell.

Set `ADMIN_ACTION_SECRET` to a long random value. The `/api/admin/servers` route requires it in
the `x-admin-secret` header and only deploys orders already marked `paid`. Do not call this endpoint
from public browser code or expose the secret.

## 4. Payment
For real QRIS automation, select a licensed payment provider/merchant account and implement its
checkout creation flow. Set the provider's server key only in server environment variables.
The included `/api/webhooks/midtrans` endpoint only handles and verifies Midtrans notifications;
it is not a complete payment integration by itself. Test callbacks and amount/order matching
before enabling live payments.

## 5. Deploy to Vercel
Import the project repository into Vercel, add the environment variables, then deploy.
Run `npm run build` locally before production deployment.

## Production checklist
- Implement authenticated customer accounts and order ownership.
- Implement authenticated admin UI and audit logs.
- Add rate limiting and abuse protection.
- Add idempotent payment processing and verify amount/currency/order ID with provider status API.
- Add refund, cancellation, expiry, renewals and support workflows.
- Test on a staging Pterodactyl panel before provisioning customer servers.
