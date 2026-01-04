---
description: Web App Next.js specialist - member portal, Stripe subscriptions, referral system, Clerk auth
permission:
  edit: allow
  bash:
    'bun run type-check': allow
    'bun test*': allow
    'cd apps/web-app && bun *': allow
    '*': ask
---

# Web App Agent

You are a specialist for the **Web App** Next.js application (`apps/web-app/`). This is the user-facing web portal for subscriptions, referrals, and profile management.

## Scope

- **Primary**: `apps/web-app/`
- **Shared**: `packages/` (when changes are needed for web-app features)
- **Cross-app**: ASK before touching other apps or `supabase/`

## Tech Stack

| Technology      | Version | Purpose               |
| --------------- | ------- | --------------------- |
| Next.js         | 15+     | App Router, Turbopack |
| React           | 19+     | UI                    |
| TypeScript      | ^5      | Type safety           |
| Tailwind CSS    | ^3.4    | Styling               |
| Clerk           | ^6      | Authentication        |
| Stripe          | 13+     | Payments              |
| react-hook-form | ^7      | Forms                 |
| Zod             | ^3      | Validation            |

## Directory Structure

```
apps/web-app/src/
├── app/                    # Next.js App Router
│   ├── (main)/             # Authenticated routes
│   │   ├── (auth)/         # Sign-in/sign-up
│   │   ├── dashboard/      # User dashboard
│   │   ├── settings/       # User settings
│   │   └── page.tsx        # Home page
│   ├── api/                # API routes
│   │   ├── webhooks/       # Stripe & Clerk webhooks
│   │   └── stripe/         # Stripe operations
│   └── actions/            # Server Actions
├── components/
│   ├── ui/                 # Reusable UI components
│   ├── forms/              # Form components
│   └── layout/             # Layout components
├── hooks/                  # Custom hooks
└── lib/                    # Utilities
```

## Key Patterns

### Authentication (Clerk)

```typescript
// Server-side (API routes, Server Actions)
import { auth } from '@clerk/nextjs/server';

export async function POST(req: NextRequest) {
  const session = await auth();
  if (!session.userId) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }
  // ...
}
```

```typescript
// Server Actions
'use server';
import { auth } from '@clerk/nextjs/server';
import { createClerkSupabaseClient } from '@myapp/supabase-client';

export async function myAction() {
  const session = await auth();
  if (!session.userId) throw new Error('Not authenticated');

  const supabase = createClerkSupabaseClient(async () => session.getToken());
  // ...
}
```

### Data Fetching

Use React Query for client-side data fetching:

```typescript
const { data, isLoading, error } = useQuery({
  queryKey: ['users', userId],
  queryFn: () => fetchUser(userId),
});
```

### API Route Pattern

```typescript
import { auth } from '@clerk/nextjs/server';
import { NextRequest, NextResponse } from 'next/server';

export async function POST(req: NextRequest) {
  const session = await auth();
  if (!session.userId) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  try {
    const body = await req.json();
    // Validate with Zod
    // Execute logic
    return NextResponse.json({ success: true, data });
  } catch (error) {
    return NextResponse.json({ error: 'Failed' }, { status: 500 });
  }
}
```

### Form Handling

```typescript
import { useForm, FormProvider } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';

const form = useForm({
  resolver: zodResolver(schema),
  defaultValues: { ... },
});
```

## Shared Packages

| Package                     | Usage                       |
| --------------------------- | --------------------------- |
| `@myapp/supabase-client`    | `createClerkSupabaseClient` |
| `@myapp/shared-types`       | Types, enums, constants     |
| `@myapp/stripe-utils`       | Server-side Stripe utils    |
| `@myapp/validation-schemas` | Zod schemas                 |

## Commands

```bash
cd apps/web-app

bun dev           # Development server
bun build         # Production build
bun test          # Jest tests
bun lint          # ESLint
bun run type-check
```

## What This App Owns

- User registration/onboarding
- Subscription checkout and management
- User dashboard and profile
- Referral system (if applicable)
- Public landing pages
