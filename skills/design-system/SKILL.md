---
name: design-system
description: Reference design system tokens, colors, typography, and component patterns. Use when doing UI work, styling, creating new components, or fixing visual issues.
---

# Design System Skill

When doing any UI or styling work, follow these guidelines.

## Before Making Changes

1. Read the design tokens from your `tailwind.config.ts` (or equivalent) and styles directory
2. Check existing components in your components directory for patterns before creating new ones
3. Use your component library (shadcn/ui, Radix, etc.) as the base — don't reinvent existing components
4. Use the `cn()` utility (or equivalent) for conditional classNames

## Rules

- IMPORTANT: Use Tailwind classes only — no inline styles or CSS modules
- IMPORTANT: All interactive elements must have hover, focus, and active states
- IMPORTANT: Use semantic HTML elements — `button` for clickable actions, NOT `div` with `onClick`
- Respect `prefers-reduced-motion` for all animations
- Mobile-first: design for mobile, then add responsive breakpoints
- All images need `alt` text
- Minimum touch target: 44x44px on mobile
- Ensure sufficient contrast ratios (4.5:1 for text, 3:1 for large text)

## Color Usage

- Read the actual color tokens from your config before using any colors
- Use semantic color names (primary, destructive, muted) not raw hex values
- Never introduce new arbitrary colors without checking existing tokens

## Component Patterns

- Server Components by default — add 'use client' only when needed
- Co-locate component, types, and tests in the same directory when possible
- Extract components when they exceed ~150 lines or are reused in 2+ places
- Check existing components before creating new ones (prevents duplication)

## Customization

Update this skill with your project-specific details:
- Replace references to `tailwind.config.ts` with your actual config path
- Add your specific color tokens and spacing scale
- Add your component directory path
- Add any project-specific UI conventions
