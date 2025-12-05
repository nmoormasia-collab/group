# GroupTherapy Record Label Website

## Overview

GroupTherapy is a full-stack web application for an electronic music record label, featuring a public-facing website for music discovery and fan engagement alongside a secure admin dashboard for content management. The platform showcases releases, artists, events, radio streams, videos, and news with a media-rich, dark mode-optimized interface inspired by major labels like Interscope and platforms like Spotify.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture

**Framework & Build Tooling**
- React 18 with TypeScript for type safety and modern component patterns
- Vite for fast development server with HMR and optimized production builds
- Wouter for lightweight client-side routing (separate route hierarchies for public and admin sections)

**UI & Styling System**
- Shadcn/UI components built on Radix UI primitives with "new-york" style configuration
- Tailwind CSS for utility-first styling with custom design tokens and CSS variables for theming
- Dark/light mode support with persistent theme preference
- Custom font stack: Space Grotesk (headlines), Geist (body text), Geist Mono (code)
- Framer Motion for page transitions, carousels, and interactive animations

**State & Data Management**
- TanStack Query (React Query) for server state management with aggressive caching strategies
- React Context for global application state (radio player, theme preferences, authentication)
- Custom hooks for reusable stateful logic (mobile detection, toast notifications)

**Form Handling & Validation**
- React Hook Form for performant form state management
- Zod schemas for runtime validation via @hookform/resolvers
- Shared validation schemas between client and server (in `shared/` directory)

### Backend Architecture

**Server Framework & Runtime**
- Express.js on Node.js (ESM modules) with TypeScript
- Dual deployment modes: local development with Vite middleware, serverless on Vercel
- RESTful API design with all routes under `/api` prefix
- Static file serving from `dist/public` with SPA fallback to `index.html`

**Build & Bundling Strategy**
- Separate build processes: Vite for client, esbuild for server
- Server dependency bundling (selective allowlist) to reduce cold start syscalls on serverless platforms
- Source maps disabled in production for smaller bundle sizes

**Deployment Architecture (Vercel)**
- Serverless function at `api/index.ts` handles all API requests
- Static assets served from `dist/public` with CDN caching
- Request routing via `vercel.json`: API routes to serverless function, static routes to files
- Environment-based initialization (database connections, session management)

### Authentication & Security

**Authentication System**
- Database-backed admin authentication with bcrypt password hashing (10 salt rounds)
- Session-based authentication with 24-hour token expiration
- Custom session management (not using connect-pg-simple in production to avoid middleware conflicts)

**Security Features**
- Rate limiting on login attempts: max 5 failed attempts per 15-minute window, then account lockout
- Login attempt tracking by username and IP address stored in database
- CORS configuration with environment-specific origins
- Security headers: X-Content-Type-Options, X-Frame-Options, X-XSS-Protection
- Trust proxy configuration for correct IP detection behind load balancers

**Admin User Management**
- Separate `admin_users` table with role-based access control (admin, editor, contributor)
- Active/inactive user flags for account management
- Last login timestamp tracking
- Seed script (`npm run seed-admin`) for initial admin user creation from environment variables

### Data Storage & Database

**Database Technology**
- PostgreSQL via Supabase with connection pooling (transaction mode)
- Drizzle ORM for type-safe database queries and schema management
- Migrations stored in `supabase/migrations/` directory, schema defined in `shared/schema.ts`

**Storage Architecture**
- Abstract `IStorage` interface defines all data operations
- DatabaseStorage implementation for PostgreSQL/Supabase (serverless-compatible)
- Single storage instance exported from `server/storage.ts`
- Database-backed sessions for serverless authentication (24-hour expiration)

**Database Schema Design**
- **users/admin_users**: Authentication and authorization with password hashes and role assignments
- **login_attempts**: Rate limiting and security audit trail
- **sessions**: Database-backed session storage for serverless authentication
- **artists**: Roster management with bios, images, Spotify IDs, social links (JSONB)
- **releases**: Album/EP/single catalog with cover art, genres (array), Spotify URLs, track listings
- **events**: Show calendar with venue details, ticket links, featured flags
- **posts**: News/blog content with rich text, images, publication status
- **videos**: Video catalog with YouTube IDs, thumbnails, categories
- **playlists**: Curated collections with Spotify URLs and track counts
- **radio_shows**: Programming schedule with hosts, descriptions, recurring show support
- **radio_settings**: Radio station configuration with current track, live status, listener count
- **contacts**: Contact form submissions with status tracking
- **page_views**: Analytics for page view tracking
- **play_counts**: Analytics for release play tracking
- **radio_listeners**: Analytics for radio listener tracking

**Data Validation**
- Zod schemas created from Drizzle tables using `drizzle-zod`
- Insert and select schemas for type-safe operations
- Shared schemas between frontend and backend for consistent validation

### Media & Asset Management

**Cloudinary Integration**
- Image, video, and audio uploads via Cloudinary API
- Environment-based configuration (VITE_CLOUDINARY_CLOUD_NAME, VITE_CLOUDINARY_UPLOAD_PRESET)
- Upload components with file type validation and size limits (images: 5MB, videos: 100MB, audio: 50MB)
- Graceful fallback when Cloudinary not configured (development mode)

**Static Assets**
- Public files served from `client/public/` (favicon, manifest, robots.txt)
- Production build outputs to `dist/public/` for Vercel deployment
- Image optimization and lazy loading with Intersection Observer

### Radio Streaming

**Live Radio Implementation**
- Audio streaming via HTML5 Audio API with custom controls
- Global persistent radio player (context-based state management)
- Live stream metadata polling for current track information
- Volume control with localStorage persistence
- Expandable player UI (mini-player and full view modes)
- Listener count simulation (can be connected to real analytics)

## External Dependencies

**Database & Infrastructure**
- **Supabase**: PostgreSQL database hosting with connection pooling and SSL support
- **Vercel**: Serverless deployment platform with edge network and automatic HTTPS
- **Cloudinary**: Media storage and CDN for images, videos, and audio files

**Third-Party APIs & Services**
- **Spotify Web API SDK** (@spotify/web-api-ts-sdk): Artist and release metadata integration
- **Zeno.FM** (or similar): Radio streaming service (demo URL currently configured)

**Authentication & Session Management**
- **bcryptjs**: Password hashing with configurable salt rounds
- Custom session storage in PostgreSQL (not using express-session middleware)

**Development & Build Tools**
- **Node.js** (>=18.0.0): Runtime environment
- **TypeScript**: Type system for both client and server
- **tsx**: TypeScript execution for scripts and development
- **Drizzle Kit**: Database migration tool and schema management
- **esbuild**: Server bundling for production
- **Vite**: Frontend build tool and development server

**UI & Component Libraries**
- **Radix UI**: Headless component primitives for accessibility
- **Tailwind CSS**: Utility-first CSS framework
- **Framer Motion**: Animation library for React
- **Lucide React**: Icon library
- **React Icons**: Additional icon sets (Spotify, social media)

**Form & Data Handling**
- **React Hook Form**: Form state management
- **Zod**: Schema validation
- **TanStack Query**: Server state and data fetching
- **date-fns**: Date manipulation and formatting

**Environment Variables Required**
- `DATABASE_URL`: PostgreSQL connection string (Supabase format)
- `SESSION_SECRET`: Random string for session encryption (min 32 characters)
- `ADMIN_USERNAME`: Initial admin username for seeding
- `ADMIN_PASSWORD`: Initial admin password for seeding
- `VITE_CLOUDINARY_CLOUD_NAME`: Cloudinary account identifier
- `VITE_CLOUDINARY_UPLOAD_PRESET`: Cloudinary upload preset name
- `NODE_ENV`: Environment mode (development/production)
- `CORS_ORIGIN`: Allowed CORS origin (optional, defaults to wildcard in production)