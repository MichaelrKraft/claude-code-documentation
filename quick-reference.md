# Quick Reference Guide

## 🎨 Design Decisions

**Colors:** Primary #2563eb, Secondary #7c3aed, Accent #059669
**Fonts:** Inter (primary), Fira Code (monospace)
**Spacing:** 4px base unit, use Tailwind scale
**Components:** Reference 21st.dev library patterns

## 🏗 Architecture Choices

**Structure:** React 18+ with TypeScript
**Database:** Supabase PostgreSQL with real-time subscriptions
**Auth:** Supabase Auth with protected routes
**State:** Context API or Zustand for complex state
**Deployment:** Vercel with GitHub integration

## 🔄 Common User Flows

**Authentication:** Sign up → Email verification → Onboarding → Dashboard
**E-commerce:** Browse → Filter → Product detail → Cart → Checkout → Confirmation
**SaaS:** Landing → Trial signup → Onboarding → Feature adoption → Subscription

## 📝 Content Patterns

**Headlines:** Action-oriented, benefit-focused
**CTAs:** "Get Started Free", "Schedule Consultation", "Download Guide"
**Copy Tone:** Professional yet friendly, clear and jargon-free

## 🚨 When User Says...

- **"Business website"** → Use Business Service template
- **"Online store"** → Use E-commerce patterns
- **"Dashboard"** → Use SaaS Dashboard architecture
- **"Portfolio"** → Use Creative Portfolio template
- **"Landing page"** → Determine business type first, then apply appropriate template

## 📱 Mobile-First Reminders

- Touch targets minimum 44px
- Hamburger menu for navigation
- Bottom navigation for apps
- Thumb-friendly interaction zones
