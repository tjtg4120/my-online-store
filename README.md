# Auto POD Store

A customizable Next.js e-commerce starter for a Printify print-on-demand business, with PayPal checkout, Supabase persistence, admin dashboard, order tracking and Printify webhook handling.

## Stack
- Next.js + React + TypeScript
- Supabase Postgres
- PayPal Orders API (server-side create/capture)
- Printify REST API + webhooks
- Plain CSS so the visual design is easy to edit

## 1. Install
```bash
npm install
cp .env.example .env.local
npm run dev
```

## 2. Supabase
Create a free Supabase project, open SQL Editor, and run `supabase/schema.sql`.
Then fill the Supabase URL, anon key and service-role key in `.env.local`.

Never expose `SUPABASE_SERVICE_ROLE_KEY` to the browser.

## 3. PayPal
Create a PayPal developer app and use sandbox credentials while testing. Set:
- `PAYPAL_CLIENT_ID`
- `PAYPAL_CLIENT_SECRET`
- `PAYPAL_ENV=sandbox`
- `STORE_BASE_URL=http://localhost:3000`

The checkout creates a PayPal Orders v2 order on the server and redirects the buyer to PayPal. After approval, `/checkout/success` calls the server capture route.

## 4. Printify
In Printify, create a Personal Access Token with the scopes needed for shops/products/orders/webhooks. Put it in `PRINTIFY_API_TOKEN` and your shop ID in `PRINTIFY_SHOP_ID`.

For automatic fulfillment, products in the database need `printify_product_id` and `printify_variant_map`, for example:
```json
{"default": 45740}
```
The checkout validates product prices against the database, then after a completed PayPal capture submits an existing Printify product order.

Set the Printify webhook URL to:
`https://YOUR-DOMAIN/api/printify/webhook`

Set `PRINTIFY_WEBHOOK_SECRET` to the same secret used for the webhook signature.

## 5. Product mapping
The fastest route is to use the Admin page's **SYNC PRINTIFY** button. It imports a small batch of Printify products and maps their default variant. For a production store, edit each product's variant map so every size/color SKU is mapped correctly.

## 6. Admin
Open `/admin` and enter `ADMIN_PASSWORD`. This starter protects admin API routes with a server-side password header. For a larger production business, replace this with Supabase Auth + MFA/RBAC.

## 7. Free/low-cost deployment
Vercel has a $0 Hobby plan, but its current terms say Hobby is for personal, non-commercial use. If this becomes a real commercial store, check the current hosting terms before launch. Supabase currently offers a Free plan with two free projects and usage quotas. Payment processing and Printify fulfillment still have their own costs/fees.

## 8. Important automation limitation
The website can automatically submit an order to Printify only when the product/variant is correctly mapped. If a product has no Printify mapping, the paid order is deliberately marked `manual-required` instead of sending a broken order.

## 9. Production checklist
- Use PayPal live credentials only after sandbox testing.
- Use HTTPS.
- Set a long random admin password or migrate admin auth to Supabase Auth.
- Configure a real transactional email provider if you want branded automated emails.
- Configure Printify webhook events and verify the signature secret.
- Test an actual end-to-end order before advertising the store.
