# Technical Architecture & Templates Guide

## Overview
This guide provides standardized technical patterns and architecture templates for building scalable, maintainable React applications using modern tools and best practices.

## Core Technology Stack

### Frontend Framework
- **React 18+**: Component-based architecture with hooks
- **TypeScript**: Type safety and better developer experience
- **Tailwind CSS**: Utility-first styling framework
- **21st.dev Components**: Pre-built, accessible component library

### Backend & Database
- **Supabase**: PostgreSQL database with real-time capabilities
- **Supabase Auth**: Authentication and user management
- **Supabase Storage**: File and media storage
- **Edge Functions**: Serverless functions for custom logic

### Development & Deployment
- **Vite**: Fast build tool and development server
- **GitHub**: Version control and collaboration
- **Vercel**: Deployment and hosting platform
- **ESLint + Prettier**: Code quality and formatting

## Project Structure Templates

### Standard React Project Structure
```
src/
├── components/           # Reusable UI components
│   ├── ui/              # Basic UI components (buttons, inputs)
│   ├── layout/          # Layout components (header, footer)
│   └── features/        # Feature-specific components
├── pages/               # Page components
├── hooks/               # Custom React hooks
├── lib/                 # Utility functions and configurations
│   ├── supabase.ts      # Supabase client configuration
│   ├── utils.ts         # General utility functions
│   └── types.ts         # TypeScript type definitions
├── stores/              # State management (Zustand/Context)
├── assets/              # Static assets (images, icons)
└── styles/              # Global styles and Tailwind config
```

### Component Organization Pattern
```
components/
├── ui/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.types.ts
│   │   └── index.ts
│   └── Input/
│       ├── Input.tsx
│       ├── Input.types.ts
│       └── index.ts
└── features/
    ├── auth/
    │   ├── LoginForm.tsx
    │   ├── SignupForm.tsx
    │   └── index.ts
    └── dashboard/
        ├── DashboardStats.tsx
        ├── DashboardTable.tsx
        └── index.ts
```

## Application Architecture Patterns

### 1. E-commerce Application
```
Core Features:
- Product catalog with search/filtering
- Shopping cart and checkout
- User authentication and profiles
- Order management
- Payment processing (Stripe integration)
- Admin dashboard

Database Schema:
- users (profiles, addresses)
- products (inventory, pricing, categories)
- orders (order_items, status tracking)
- categories (hierarchical structure)
- reviews (ratings, comments)

Key Components:
- ProductGrid, ProductCard, ProductDetails
- ShoppingCart, CheckoutForm
- UserProfile, OrderHistory
- AdminProductManager, OrderDashboard
```

### 2. SaaS Dashboard Application
```
Core Features:
- User authentication and team management
- Subscription and billing
- Data visualization and analytics
- Settings and configuration
- API integrations

Database Schema:
- users (roles, permissions)
- organizations (teams, subscriptions)
- projects (data, configurations)
- analytics (events, metrics)
- billing (plans, invoices)

Key Components:
- DashboardLayout, Sidebar, TopNav
- DataTable, Charts, Metrics
- UserManagement, TeamSettings
- BillingDashboard, SubscriptionManager
```

### 3. Portfolio/Creative Showcase
```
Core Features:
- Project galleries with media
- About/bio sections
- Contact forms
- Blog/news updates
- Client testimonials

Database Schema:
- projects (images, descriptions, categories)
- blog_posts (content, tags, publishing)
- testimonials (client feedback)
- contact_submissions (inquiries)

Key Components:
- ProjectGallery, ProjectDetail
- BlogList, BlogPost
- ContactForm, TestimonialSlider
- AboutSection, HeroSection
```

### 4. Business Service Website
```
Core Features:
- Service descriptions and pricing
- Lead generation forms
- Appointment booking
- Team/staff profiles
- Testimonials and case studies

Database Schema:
- services (descriptions, pricing)
- appointments (scheduling, availability)
- leads (contact information, source)
- team_members (profiles, specialties)
- case_studies (projects, results)

Key Components:
- ServiceCard, PricingTable
- BookingCalendar, ContactForm
- TeamGrid, TestimonialSection
- CaseStudyDetail, LeadDashboard
```

## Database Schema Patterns

### User Management Pattern
```sql
-- Users table (extends Supabase auth.users)
CREATE TABLE profiles (
  id UUID REFERENCES auth.users ON DELETE CASCADE,
  email TEXT UNIQUE NOT NULL,
  full_name TEXT,
  avatar_url TEXT,
  role TEXT DEFAULT 'user',
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  PRIMARY KEY (id)
);

-- Organizations for team/business accounts
CREATE TABLE organizations (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Organization memberships
CREATE TABLE organization_members (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  organization_id UUID REFERENCES organizations(id) ON DELETE CASCADE,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  role TEXT DEFAULT 'member',
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  UNIQUE(organization_id, user_id)
);
```

### Content Management Pattern
```sql
-- Flexible content structure
CREATE TABLE content (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  title TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  content JSONB,
  status TEXT DEFAULT 'draft',
  type TEXT NOT NULL, -- 'page', 'post', 'product', etc.
  author_id UUID REFERENCES profiles(id),
  published_at TIMESTAMP WITH TIME ZONE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Categories and tags
CREATE TABLE categories (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  parent_id UUID REFERENCES categories(id),
  sort_order INTEGER DEFAULT 0
);

CREATE TABLE content_categories (
  content_id UUID REFERENCES content(id) ON DELETE CASCADE,
  category_id UUID REFERENCES categories(id) ON DELETE CASCADE,
  PRIMARY KEY (content_id, category_id)
);
```

## Authentication Patterns

### Supabase Auth Setup
```typescript
// lib/supabase.ts
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL!
const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!

export const supabase = createClient(supabaseUrl, supabaseAnonKey)

// Auth helper functions
export const signUp = async (email: string, password: string) => {
  const { data, error } = await supabase.auth.signUp({
    email,
    password,
  })
  return { data, error }
}

export const signIn = async (email: string, password: string) => {
  const { data, error } = await supabase.auth.signInWithPassword({
    email,
    password,
  })
  return { data, error }
}

export const signOut = async () => {
  const { error } = await supabase.auth.signOut()
  return { error }
}
```

### Protected Route Pattern
```typescript
// hooks/useAuth.ts
import { useEffect, useState } from 'react'
import { User } from '@supabase/supabase-js'
import { supabase } from '@/lib/supabase'

export const useAuth = () => {
  const [user, setUser] = useState<User | null>(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    // Get initial session
    const getSession = async () => {
      const { data: { session } } = await supabase.auth.getSession()
      setUser(session?.user ?? null)
      setLoading(false)
    }

    getSession()

    // Listen for auth changes
    const { data: { subscription } } = supabase.auth.onAuthStateChange(
      (event, session) => {
        setUser(session?.user ?? null)
        setLoading(false)
      }
    )

    return () => subscription.unsubscribe()
  }, [])

  return { user, loading }
}
```

## API Integration Patterns

### Supabase Data Fetching
```typescript
// hooks/useData.ts
import { useEffect, useState } from 'react'
import { supabase } from '@/lib/supabase'

export const useData = <T>(
  table: string,
  query?: string,
  dependencies: any[] = []
) => {
  const [data, setData] = useState<T[]>([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true)
        let queryBuilder = supabase.from(table).select(query || '*')
        
        const { data, error } = await queryBuilder
        
        if (error) throw error
        
        setData(data || [])
        setError(null)
      } catch (err) {
        setError(err instanceof Error ? err.message : 'An error occurred')
      } finally {
        setLoading(false)
      }
    }

    fetchData()
  }, dependencies)

  return { data, loading, error }
}
```

### Real-time Subscriptions
```typescript
// hooks/useRealtimeData.ts
import { useEffect, useState } from 'react'
import { supabase } from '@/lib/supabase'

export const useRealtimeData = <T>(table: string, filter?: string) => {
  const [data, setData] = useState<T[]>([])

  useEffect(() => {
    // Initial fetch
    const fetchInitialData = async () => {
      const { data: initialData } = await supabase
        .from(table)
        .select('*')
      
      if (initialData) setData(initialData)
    }

    fetchInitialData()

    // Set up real-time subscription
    const subscription = supabase
      .channel(`${table}_changes`)
      .on('postgres_changes', 
        { event: '*', schema: 'public', table, filter },
        (payload) => {
          console.log('Change received!', payload)
          // Handle INSERT, UPDATE, DELETE
          // Update local state accordingly
        }
      )
      .subscribe()

    return () => {
      supabase.removeChannel(subscription)
    }
  }, [table, filter])

  return data
}
```

## Performance Optimization

### Code Splitting
```typescript
// Lazy load components
import { lazy, Suspense } from 'react'

const Dashboard = lazy(() => import('@/pages/Dashboard'))
const Profile = lazy(() => import('@/pages/Profile'))

// Use with Suspense
<Suspense fallback={<LoadingSpinner />}>
  <Dashboard />
</Suspense>
```

### Image Optimization
```typescript
// Use Next.js Image component or similar optimization
import Image from 'next/image'

<Image
  src="/hero-image.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority
  className="object-cover"
/>
```

### Caching Strategies
```typescript
// React Query for server state management
import { useQuery } from '@tanstack/react-query'

export const useProducts = () => {
  return useQuery({
    queryKey: ['products'],
    queryFn: async () => {
      const { data } = await supabase
        .from('products')
        .select('*')
      return data
    },
    staleTime: 5 * 60 * 1000, // 5 minutes
  })
}
```

## Development Workflow

### Environment Configuration
```bash
# .env.local
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
```

### Git Workflow
```bash
# Feature branch workflow
git checkout -b feature/user-authentication
git add .
git commit -m "feat: implement user authentication"
git push origin feature/user-authentication
# Create pull request on GitHub
```

### Deployment Pipeline
```yaml
# .github/workflows/deploy.yml
name: Deploy to Vercel
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
```

This technical architecture guide ensures consistent, scalable, and maintainable applications using modern React patterns and best practices.
