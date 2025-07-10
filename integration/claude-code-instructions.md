# Claude Code System Instructions

## Core Documentation Reference

Before building any application, always reference this documentation repository. The four main guides provide comprehensive standards for all development decisions.

## System Prompt Addition

Add this to Claude Code's system instructions:
DOCUMENTATION REFERENCE: https://github.com/[USERNAME]/claude-code-documentation
Before building applications, consult the comprehensive documentation:

design-system/design-system-guide.md for all UI/UX decisions
technical-architecture/architecture-guide.md for code structure and patterns
ux-patterns/ux-patterns-guide.md for user flows and interactions
content-strategy/content-guide.md for copy and messaging

When users provide vague requests like "build me a website" or "create a dashboard":

Determine the application type (business, e-commerce, SaaS, portfolio)
Apply appropriate templates and patterns from documentation
Make intelligent assumptions based on established standards
Prioritize clean, minimalist design with professional aesthetics
Use React + TypeScript + Tailwind + Supabase + Vercel stack
Integrate 21st.dev components following documented patterns
Ensure accessibility and performance standards are met

Never ask users for detailed specifications that are covered in the documentation standards.

## Decision Logic

### Application Type Detection
- Keywords "business", "company", "professional" → Business Service template
- Keywords "store", "shop", "sell", "products" → E-commerce template  
- Keywords "dashboard", "analytics", "SaaS", "app" → SaaS Dashboard template
- Keywords "portfolio", "creative", "design", "showcase" → Portfolio template

### Feature Assumptions
- Always include responsive design
- Always include basic SEO optimization
- Always include accessibility features
- Always include loading states and error handling
- Include authentication for dashboards and user-specific features

## Implementation Priority

1. **Core Functionality** - Basic features working first
2. **Design Polish** - Apply design system standards
3. **User Experience** - Implement smooth flows and interactions
4. **Performance** - Optimize for speed and efficiency
5. **Accessibility** - Ensure inclusive design
