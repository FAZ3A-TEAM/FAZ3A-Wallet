# FAZ3A Wallet - Professional Digital Wallet System

<div align="center">

![FAZ3A Wallet](https://img.shields.io/badge/FAZ3A-Wallet-green?style=for-the-badge)
![Next.js](https://img.shields.io/badge/Next.js-16-black?style=for-the-badge&logo=next.js)
![TypeScript](https://img.shields.io/badge/TypeScript-5.9-blue?style=for-the-badge&logo=typescript)
![Stripe](https://img.shields.io/badge/Stripe-Payment-purple?style=for-the-badge&logo=stripe)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-blue?style=for-the-badge&logo=postgresql)

A production-ready, full-stack digital wallet application with modern UI/UX, secure authentication, card management, client management, and comprehensive payment processing.

**Live:** [https://wallet.faz3a.info](https://wallet.faz3a.info)

[Features](#features) • [Installation](#installation) • [API](#api-endpoints) • [Deployment](#production-deployment) • [Documentation](#documentation)

</div>

---

## 🚀 Features

### 🔐 Authentication & Security
- **NextAuth.js Integration**: Secure session management with JWT
- **User Registration & Login**: Email/password authentication
- **Password Security**: Bcrypt hashing with automatic legacy password migration
- **Protected Routes**: Automatic authentication checks
- **Role-Based Access**: USER, ADMIN, SUPPORT_STAFF roles

### 💳 Card Management
- **Save Multiple Cards**: Store unlimited payment methods via Stripe
- **Default Card Selection**: Set and manage preferred payment method
- **Card Brand Detection**: Automatic Visa, Mastercard, Amex, Discover recognition
- **Stripe Integration**: Secure tokenization and PCI compliance
- **Virtual Card Issuance**: Issue Stripe virtual cards to users

### 💰 Wallet Operations
- **Real-time Balance**: Live wallet balance tracking
- **Quick Top-Up**: Instant wallet recharge using saved cards
- **Transaction History**: Complete audit trail of all transactions
- **Client Management**: Manage multiple clients with individual balances
- **Money Transfers**: Send money between clients
- **Withdrawals**: Bank withdrawal requests with status tracking

### 🎨 Modern UI/UX
- **Shadcn/ui Components**: Professional, accessible component library
- **Responsive Design**: Mobile-first, works on all devices
- **Dark Mode**: Automatic theme detection with manual toggle
- **Loading States**: Professional shimmer animations
- **Toast Notifications**: User-friendly feedback system
- **3D Graphics**: Three.js integration for enhanced visuals

### 📊 Dashboard & Analytics
- **Personalized Dashboard**: User-specific overview
- **Balance Overview**: Prominent balance display
- **Recent Transactions**: Quick access to latest activity
- **Client Overview**: Manage and view client balances
- **Transaction Export**: PDF generation for transaction history

---

## 🛠 Tech Stack

### Frontend
- **Framework**: Next.js 16 (App Router)
- **Language**: TypeScript 5.9
- **Styling**: Tailwind CSS 4.1
- **UI Library**: Shadcn/ui (Radix UI)
- **Icons**: Lucide React
- **Forms**: Stripe Elements
- **3D Graphics**: Three.js, React Three Fiber
- **Animations**: Framer Motion
- **PDF Generation**: jsPDF, jsPDF AutoTable

### Backend
- **API**: Next.js API Routes
- **ORM**: Prisma 5.22
- **Database**: PostgreSQL (Neon)
- **Authentication**: NextAuth.js 4.24
- **Password Hashing**: Bcrypt.js

### Payment Processing
- **Provider**: Stripe 20.0
- **Features**: 
  - Payment Intents
  - Setup Intents
  - Payment Methods
  - Virtual Card Issuance
  - Webhooks
- **Security**: Webhook signature verification

### Deployment
- **Platform**: Vercel
- **Database**: Neon PostgreSQL
- **Environment**: Production-optimized

---

## 🚀 Quick Start

### Prerequisites

- **Node.js** 18+ 
- **npm** or **pnpm**
- **PostgreSQL Database** (Neon recommended)
- **Stripe Account** ([Sign up](https://dashboard.stripe.com/register))


### Stripe Webhook Testing (Local)

For local webhook testing:

```bash
# Install Stripe CLI
# https://stripe.com/docs/stripe-cli

# Forward webhooks to local server
stripe listen --forward-to localhost:3000/api/stripe-webhook
```

Copy the webhook secret and add to `.env.local`:
```env
STRIPE_WEBHOOK_SECRET="whsec_..."
```

### Card Management

#### `POST /api/setup-intent`
Create setup intent for saving a card.

#### `GET /api/saved-cards?email=user@example.com`
Get all saved cards.

#### `POST /api/saved-cards`
Save a new payment method.

#### `DELETE /api/saved-cards?cardId=xxx&email=user@example.com`
Remove a saved card.

#### `POST /api/set-default-card`
Set a card as default.

#### `POST /api/issue-card`
Issue a virtual card to user.

### Client Management

#### `GET /api/clients?email=user@example.com`
Get all clients.

#### `POST /api/clients`
Create a new client.

#### `GET /api/clients/[id]`
Get client details.

#### `PUT /api/clients/[id]`
Update client.

#### `DELETE /api/clients/[id]`
Delete client.

#### `POST /api/clients/transact`
Process transaction between clients.

### Transfers & Withdrawals

#### `POST /api/transfer`
Transfer money between accounts.

#### `GET /api/withdrawals?email=user@example.com`
Get withdrawal history.

#### `POST /api/withdrawals`
Create withdrawal request.

### Webhooks

#### `POST /api/stripe-webhook`
Handle Stripe webhook events.

**Events:**
- `payment_intent.succeeded`
- `payment_intent.payment_failed`

#### `POST /api/stripe-issuing-authorization`
Handle Stripe Issuing real-time authorization requests.

**Purpose:** Programmatically control cardholder purchase approvals through synchronous webhook endpoint.

**Event:** `issuing_authorization.request`

**Response Format:**
- Approve: `{"approved": true}` with `200` status
- Decline: `{"approved": false}` with `200` status

**Response Time:** Must respond within ~2 seconds

**Configuration:**
1. Go to Stripe Dashboard → Settings → Issuing → Authorization controls
2. Add endpoint: `https://your-domain.com/api/stripe-issuing-authorization`
3. Select payload format:
   - **Snapshot (API v1)**: Complete resource object (faster, no additional API calls)
   - **Thin (API v2)**: Only resource ID (requires additional API call for full data)

**Authorization Logic:**
- Checks if card is active
- Verifies sufficient user balance
- Enforces daily spending limits ($1000 default)
- Blocks restricted merchant categories (gambling, adult entertainment)
- Declines on any error for security

**Note:** This endpoint uses the same `STRIPE_WEBHOOK_SECRET` as the main webhook endpoint.

---

## 🗄️ Database Schema

### Models

- **User**: User accounts with authentication and balance
- **Transaction**: All financial transactions
- **SavedCard**: Saved payment methods
- **IssuedCard**: Virtual cards issued to users
- **Client**: Client management for businesses
- **Withdrawal**: Bank withdrawal requests

See `prisma/schema.prisma` for complete schema definition.

---

## 🚀 Production Deployment

### Vercel Deployment

1. **Set Environment Variables in Vercel Dashboard**

   Go to **Settings** > **Environment Variables** and add:

   **Required:**
   - `DATABASE_URL` - PostgreSQL connection string
   - `POSTGRES_PRISMA_URL` - Connection pool URL
   - `POSTGRES_URL_NON_POOLING` - Direct connection URL
   - `NEXTAUTH_SECRET` - Secret key (min 32 chars)
   - `NEXTAUTH_URL` - Production URL (e.g., `https://wallet.faz3a.info`)
   - `STRIPE_SECRET_KEY` - Stripe secret key
   - `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` - Stripe publishable key
   - `STRIPE_WEBHOOK_SECRET` - Webhook signing secret

   **Optional:**
   - `NEXT_PUBLIC_STRIPE_CVC` - CVC override for testing
   - Other variables as needed

2. **Deploy to Vercel**

   ```bash
   # Via Vercel CLI
   vercel --prod
   
   # Or connect GitHub repo for automatic deployments
   ```

3. **Configure Stripe Webhook**

   - Go to [Stripe Dashboard > Webhooks](https://dashboard.stripe.com/webhooks)
   - Add endpoint: `https://wallet.faz3a.info/api/stripe-webhook`
   - Select events:
     - `payment_intent.succeeded`
     - `payment_intent.payment_failed`
   - Copy webhook secret to environment variables

4. **Run Database Migrations**

   ```bash
   npx prisma migrate deploy
   ```

### Production Checklist

- [x] Environment variables set in Vercel
- [x] Database migrations run
- [x] Stripe webhook configured
- [x] `NEXTAUTH_URL` matches production domain
- [x] Stripe keys are from same account
- [x] SSL/HTTPS enabled
- [x] Error monitoring configured

---

## 🧪 Testing

### Stripe Test Cards

| Card Number | Brand | Result |
|------------|-------|--------|
| 4242 4242 4242 4242 | Visa | ✅ Success |
| 5555 5555 5555 4444 | Mastercard | ✅ Success |
| 3782 822463 10005 | Amex | ✅ Success |
| 4000 0000 0000 0002 | Visa | ❌ Declined |
| 4000 0000 0000 9995 | Visa | ❌ Insufficient funds |

**Test Details:**
- **Expiry**: Any future date (e.g., 12/28)
- **CVC**: Any 3 digits (e.g., 123)
- **ZIP**: Any 5 digits (e.g., 12345)

More test cards: [Stripe Testing Documentation](https://stripe.com/docs/testing)

---

## 📚 Documentation

- [Production Audit Report](./docs/PRODUCTION_AUDIT_COMPLETE.md)
- [Deployment Execution Summary](./docs/DEPLOYMENT_EXECUTION_SUMMARY.md)
- [Final Deployment Guide](./docs/FINAL_DEPLOYMENT_READY.md)

---

## 🔒 Security Features

- ✅ **Password Hashing**: Bcrypt with automatic migration
- ✅ **Webhook Verification**: Stripe signature validation
- ✅ **Environment Variables**: No hardcoded secrets
- ✅ **Protected Routes**: Authentication required
- ✅ **Error Handling**: Secure error messages in production
- ✅ **SQL Injection Protection**: Prisma ORM
- ✅ **XSS Protection**: React automatic escaping
- ✅ **CSRF Protection**: NextAuth.js built-in

---

### Stripe Webhook Issues

1. Verify `STRIPE_WEBHOOK_SECRET` is set
2. Check webhook endpoint URL in Stripe Dashboard
3. Verify webhook events are selected
4. Check Vercel function logs

### Authentication Issues

1. Verify `NEXTAUTH_SECRET` is set (min 32 chars)
2. Check `NEXTAUTH_URL` matches production domain exactly
3. Clear browser cookies and try again

---

## 📄 License

Private - All rights reserved

---

## 👥 Support

For support, contact: [support@faz3a.info](mailto:support@faz3a.info)

---

## 🎯 Roadmap

- [ ] Multi-currency support
- [ ] Recurring payments
- [ ] Advanced analytics
- [ ] Mobile app
- [ ] API rate limiting
- [ ] Two-factor authentication

---

<div align="center">

**Built with FAZ3A TEAM using Next.js, TypeScript, and Stripe**

[Documentation](./docs/) • [API Reference](#api-endpoints) • [Deployment Guide](./docs/FINAL_DEPLOYMENT_READY.md)

</div>

