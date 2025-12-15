# TODOLIST App: Fullstack SaaS Project

Next.js and Supabase, featuring AI-powered task labeling, image attachments, and Stripe subscriptions.

## Features

- Task management with automatic AI labeling
- Image attachments with cloud storage
- Google OAuth and email authentication
- Premium subscription with Stripe integration
- Usage limits and tracking

## Tech Stack

- Frontend: Next.js, React, TypeScript, Tailwind CSS
- Backend: Supabase (PostgreSQL, Auth, Storage, Edge Functions)
- AI: OpenAI
- Payments: Stripe

## Quick Start

### Prerequisites

- Node.js and npm
- Supabase CLI
- Stripe CLI (optional)
- Account credentials for:
  - Supabase
  - OpenAI
  - Stripe (test mode)

### OpenAI Setup

1. Get API key from platform.openai.com
2. Set as Supabase secret:

```bash
supabase secrets set OPENAI_API_KEY=your-key
```

### Stripe Setup

1. Create Stripe test account and get API keys

2. Create subscription product:

```bash
stripe prices create \
  --currency=usd \
  --unit-amount=1000 \
  -d "recurring[interval]"=month \
  -d "recurring[trial_period_days]"=14 \
  -d "product_data[name]"="TaskMaster Premium"
```

3. Configure customer portal:

```bash
stripe billing_portal configurations create \
  -d "business_profile[privacy_policy_url]=https://your-site.com/privacy" \
  -d "business_profile[terms_of_service_url]=https://your-site.com/terms" \
  -d "default_return_url=http://localhost:3000/profile" \
  -d "features[subscription_cancel][enabled]=true" \
  -d "features[payment_method_update][enabled]=true"
```

4. Set up webhook in Stripe Dashboard:
   - Endpoint: `https://[PROJECT_ID].supabase.co/functions/v1/stripe-webhook`
   - Events: `checkout.session.completed`, `customer.subscription.deleted`, `customer.subscription.updated`

5. Set up Stripe secret in Supabase PostgresSQL vault. Go into your Supabase dashboard, to go **SQL Editor** then run this command (replacing your secret key):

```sql
insert into vault.secrets (name, secret)
select 'stripe', 'sk_test_xxx'
returning key_id;
```

### Required Environment Variables

Add to `.env.local` and `.env.test.local`:

```bash
# Add this to both:
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key

# Only .env.test.local needs these:
SUPABASE_SERVICE_KEY=your-service-key
STRIPE_SECRET_KEY=sk_test_...
STRIPE_PRICE_ID=price_...
STRIPE_WEBHOOK_SECRET=whsec_...
```


## Project Structure

```text
taskapp/
├── app/               # Next.js pages and layouts
├── components/        # React components
├── hooks/             # Application logic
│   ├── useAuth.ts     # Authentication
│   ├── useTask.ts     # Task management
│   └── useUser.ts     # User profile/subscription
├── supabase/
│   ├── functions/     # Edge Functions
│   └── migrations/    # Database migrations
└── tests/
    └── integration/   # Integration tests
```
