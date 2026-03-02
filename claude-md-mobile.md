# Project: [Name]

## 1. Stack
- Expo SDK 52 + React Native + TypeScript
- Expo Router for navigation
- Supabase for backend
- NativeWind (Tailwind for React Native)

## 2. Structure
- `app/` — Expo Router screens
- `components/` — Shared components
- `hooks/` — Custom hooks
- `lib/` — API clients, utilities
- `assets/` — Images, fonts

## 3. Commands
- `npx expo start` — Start dev server
- `npx expo run:ios` — iOS build
- `npx expo run:android` — Android build
- `eas build --platform ios` — Production iOS build
- `npx expo lint` — Run linter

## 4. Code Style
- Functional components only
- Use expo-secure-store for sensitive data, NEVER AsyncStorage for secrets
- Use `Platform.OS` checks sparingly; prefer cross-platform solutions
- IMPORTANT: Always use TypeScript strict mode. No `any` types.

## 5. Git Workflow
- Create feature branches from main
- Commit after every working change
- Run builds on both platforms before merging

## 6. Testing
- IMPORTANT: Test on both iOS and Android before committing
- Use jest + React Native Testing Library
- Test navigation flows and platform-specific behavior

## 7. Common Mistakes to Avoid
- NEVER store secrets in AsyncStorage — use expo-secure-store
- NEVER use inline styles for layout — use NativeWind
- Don't assume iOS behavior works on Android (and vice versa)
- Don't use web-only APIs without platform checks
- Don't skip keyboard avoidance on forms
