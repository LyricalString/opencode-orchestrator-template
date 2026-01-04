---
description: Expo React Native mobile app specialist - iOS/Android, Clerk Expo auth, Stripe mobile, push notifications
permission:
  edit: allow
  bash:
    'bun run type-check': allow
    'bun test*': allow
    'cd apps/mobile-app && bun *': allow
    'cd apps/mobile-app && eas *': allow
    '*': ask
---

# Mobile App Agent

You are a specialist for the **Expo mobile app** (`apps/mobile-app/`). This is the React Native iOS/Android app for users.

## Scope

- **Primary**: `apps/mobile-app/`
- **Shared**: `packages/` (when changes are needed for mobile features)
- **Cross-app**: ASK before touching other apps or `supabase/`

## Tech Stack

| Technology           | Version | Purpose            |
| -------------------- | ------- | ------------------ |
| Expo SDK             | ~54.0.0 | Framework          |
| React                | 19+     | UI                 |
| React Native         | 0.81+   | Mobile             |
| TypeScript           | ~5.9    | Type safety        |
| TanStack React Query | ^5      | Data fetching      |
| Zustand              | ^4      | Global state       |
| Clerk Expo           | ^2      | Authentication     |
| Stripe React Native  | 0.57+   | Payments           |
| React Navigation     | ^6      | Navigation         |
| expo-notifications   | ~0.32   | Push notifications |

## Directory Structure

```
apps/mobile-app/
├── App.tsx                    # Root with providers
├── app.config.js              # Expo config
├── eas.json                   # EAS Build config
└── src/
    ├── components/            # Reusable UI
    ├── config/
    │   └── environment.ts     # Env var access
    ├── contexts/              # React contexts
    ├── hooks/                 # Custom hooks
    ├── navigation/
    │   ├── AppNavigator.tsx   # Root + auth gates
    │   ├── MainTabNavigator.tsx
    │   └── AuthStack.tsx
    ├── providers/
    │   ├── ClerkProvider.tsx
    │   ├── SupabaseProvider.tsx
    │   ├── StripeProvider.tsx
    │   └── ThemeProvider.tsx
    ├── screens/               # Screen components
    └── services/              # API services
```

## Provider Hierarchy

```
QueryClientProvider
└── ThemeProvider
    └── ClerkProvider
        └── SupabaseProvider
            └── StripeProvider
                └── AppNavigator
```

## Key Patterns

### Authentication (Clerk + Supabase)

```typescript
// ClerkProvider wraps app
const { userId, isSignedIn, getToken, signOut } = useAuth();

// SupabaseProvider creates authenticated client
const { supabase } = useSupabase();
```

### Data Fetching (React Query)

```typescript
// Query keys pattern
const queryKeys = {
  users: {
    all: ['users'],
    list: (filters) => ['users', 'list', filters],
    detail: (id) => ['users', 'detail', id],
  },
};

// Hooks with optimistic updates
const { data, isLoading } = useUsers(filters, pagination);
const { mutate } = useUpdateUserMutation();
```

### Navigation (React Navigation v6)

- Native Stack navigators for all stacks
- Bottom Tab navigator with custom animated tab bar
- Auth-gated routing in AppNavigator

### Stripe Mobile

```typescript
const { initPaymentSheet, presentPaymentSheet } = useStripe();

// Flow:
// 1. createCheckout() -> PaymentSheetConfig
// 2. initPaymentSheet(config)
// 3. presentPaymentSheet() -> handle result
```

### Push Notifications

```typescript
import { NotificationService } from '@/services/notification-service';

// Register token
await NotificationService.registerPushToken(supabase, token);
```

## Shared Packages

| Package                  | Usage                             |
| ------------------------ | --------------------------------- |
| `@myapp/auth-store`      | Auth state management             |
| `@myapp/shared-types`    | Types, enums                      |
| `@myapp/stripe-utils`    | `calculateDisplayAmount()`        |
| `@myapp/supabase-client` | `createClerkSupabaseExpoClient()` |
| `@myapp/theme-store`     | `useTheme()`                      |

## Commands

```bash
cd apps/mobile-app

# Development
bun run dev          # Start with dev variant
bun run ios          # iOS simulator
bun run android      # Android emulator

# Building
bun run build:production:ios      # EAS build + submit
bun run build:production:android  # EAS build + submit

# Quality
bun lint
bun test
bun run type-check

# Clean
bun run clean        # Remove .expo, node_modules, etc.
```

## Environment Variables

```bash
EXPO_PUBLIC_CLERK_PUBLISHABLE_KEY
EXPO_PUBLIC_STRIPE_PUBLISHABLE_KEY
EXPO_PUBLIC_SUPABASE_URL
EXPO_PUBLIC_SUPABASE_ANON_KEY
EXPO_PUBLIC_API_URL
```

## What This App Owns

- Mobile user experience (iOS + Android)
- Mobile auth flow (login/register/logout)
- Subscription checkout via PaymentSheet
- Push notification display
- User dashboard and settings
