# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

FUNDAMENTAL is a Japanese-language landing page MVP for a custom acrylic merchandise service (fandom goods: keychains, stands, badges, etc.), manufactured in China and shipped directly to Japan. It is a single-page marketing site with a contact/quote request form that emails the operator via [Resend](https://resend.com).

## Commands

```bash
npm run dev      # Start dev server (http://localhost:3000)
npm run build    # Production build
npm run lint     # ESLint via next lint
```

There are no tests.

## Environment Variables

Copy `.env.example` to `.env.local` and fill in:

| Variable | Purpose |
|---|---|
| `RESEND_API_KEY` | Resend API key (from resend.com dashboard) |
| `RESEND_FROM_EMAIL` | Sender address — must be a verified Resend domain; use `onboarding@resend.dev` for local testing |
| `CONTACT_EMAIL` | Recipient address for quote-request emails |

The API route (`app/api/contact/route.ts`) returns HTTP 503 if any of these three are missing, so the form won't send in dev without them set.

## Architecture

The project is a Next.js 14 App Router app with essentially three files doing all the work:

- **`app/page.tsx`** — Single page, all sections inline (Hero, Trust stats, Products grid, Order flow, Rules/disclaimer, Contact). Static data (`products`, `steps`) is defined as `as const` arrays at the top of this file.
- **`components/ContactForm.tsx`** — `"use client"` component. Uses `react-hook-form` for validation and POSTs JSON to `/api/contact`. Manages its own submitted/error state.
- **`app/api/contact/route.ts`** — Next.js Route Handler (`POST`). Validates required fields server-side, then calls Resend to send an HTML email to the operator with `replyTo` set to the customer's email. All user input is HTML-escaped via a local `escapeHtml` helper before being templated into the email body.

## Design System

Two custom Tailwind colors defined in `tailwind.config.ts`:

- `ink` → `#0a0a0a` (near-black background used everywhere)
- `gold` → `#c9a84c` (accent color for CTAs, prices, headings)

The body font stack in `globals.css` prioritizes Japanese system fonts (`Hiragino Sans`, `Hiragino Kaku Gothic ProN`, `Noto Sans JP`, `Yu Gothic`) before falling back to generic sans-serif. All copy in the UI is in Japanese.

The visual language is dark/luxury minimal: `bg-ink`, `border-white/10`, subtle `bg-white/[0.02]` card fills, generous letter-spacing (`tracking-[0.35em]`), and gold accents.
