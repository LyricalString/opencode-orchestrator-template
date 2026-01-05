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

| Technology           | Purpose                      |
| -------------------- | ---------------------------- |
| Expo                 | React Native framework       |
| React Native         | Mobile UI                    |
| TypeScript           | Type safety                  |
| TanStack React Query | Server state & data fetching |
| Zustand              | Client-side global state     |
| Clerk Expo           | Authentication               |
| Stripe React Native  | Payments                     |
| React Navigation     | Navigation                   |
| expo-notifications   | Push notifications           |

<!-- Check package.json for current versions -->

## Directory Structure

```
apps/mobile-app/
├── App.tsx                    # Root with providers
├── app.config.js              # Expo config
├── eas.json                   # EAS Build config
└── src/
    ├── components/            # Reusable UI components
    ├── config/                # Environment and app config
    ├── contexts/              # React contexts
    ├── hooks/                 # Custom hooks
    ├── navigation/            # Navigator components (stacks, tabs)
    ├── providers/             # Context providers (auth, theme, etc.)
    ├── screens/               # Screen components
    └── services/              # API and external services
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

- Clerk handles user authentication (sign in/up/out)
- Supabase client is authenticated using Clerk tokens
- Auth state available via hooks from providers
- Check `src/providers/` for implementation details

### Data Fetching (React Query)

- All server data fetched via React Query hooks
- Query keys follow hierarchical pattern: `[entity, scope, params]`
- Custom hooks wrap queries with proper typing
- Mutations use optimistic updates where appropriate
- Check `src/hooks/` for query hook patterns

### Navigation (React Navigation)

- Native Stack navigators for screen stacks
- Bottom Tab navigator for main app sections
- Auth-gated routing (unauthenticated users see auth screens)
- Check `src/navigation/` for navigator structure

### Stripe Mobile

- Uses PaymentSheet for checkout flow
- Flow: create intent on backend → init sheet → present → handle result
- Check `src/providers/` and `src/services/` for implementation

### Push Notifications

- expo-notifications for receiving/displaying notifications
- Push tokens registered with backend on app start
- Check `src/services/` for notification handling

## Shared Packages

| Package                  | Purpose                       |
| ------------------------ | ----------------------------- |
| `@myapp/auth-store`      | Auth state management         |
| `@myapp/shared-types`    | Shared types and constants    |
| `@myapp/stripe-utils`    | Stripe helpers and formatting |
| `@myapp/supabase-client` | Supabase client creation      |
| `@myapp/theme-store`     | Theme state and hooks         |

<!-- Replace @myapp with your actual package scope -->

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

---

## Keeping This Agent Updated

**This agent definition must stay in sync with the codebase.**

Update this file when:

- Directory structure changes significantly
- New architectural patterns are introduced
- New shared packages are added or removed
- New integrations are added (auth, payments, etc.)

**Do NOT include in this file:**

- Version numbers (check package.json when needed)
- Specific function/class/hook names (searchable in code)
- Config values or API endpoints
- Environment variable values (only list names)
- Specific file names within directories (describe the pattern instead)

**Keep focus on patterns and conventions, not implementation specifics.**
