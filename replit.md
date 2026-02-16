# Replit Agent Guide - طريج (Tareej)

## Overview

Tareej (طريج) is a ride-hailing mobile application built with Expo/React Native for the frontend and Express.js for the backend. The app is designed for an Arabic-speaking audience (Iraqi dialect) and provides ride-requesting functionality similar to Uber/Careem. It includes three main screens: ride requesting, ride history, and an admin dashboard. The backend manages drivers, ride requests, pricing, and commission calculations.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend (Expo/React Native)
- **Framework**: Expo SDK 54 with React Native 0.81, using the new architecture
- **Routing**: expo-router with file-based routing and typed routes. The app uses a tab-based layout (`app/(tabs)/`) with three tabs: ride request (index), history, and admin
- **State Management**: TanStack React Query for server state management. The query client is configured in `lib/query-client.ts` with API request helpers
- **UI Libraries**: react-native-reanimated for animations, expo-linear-gradient for gradients, expo-haptics for haptic feedback, expo-blur for blur effects, expo-glass-effect for liquid glass (iOS 26+)
- **Fonts**: Cairo font family (Google Fonts) for Arabic text support - loaded via `@expo-google-fonts/cairo`
- **RTL Support**: I18nManager.allowRTL is enabled for right-to-left Arabic layout
- **Platform Support**: iOS, Android, and Web (with platform-specific adaptations throughout)

### Backend (Express.js)
- **Runtime**: Node.js with TypeScript, compiled via tsx (dev) or esbuild (prod)
- **Server**: Express 5 running on port 5000 (configured in `server/index.ts`)
- **API Prefix**: All routes are prefixed with `/api/` (e.g., `/api/request-ride`)
- **Data Storage**: Currently uses **in-memory storage** for both drivers and rides (arrays in `server/routes.ts`). A `MemStorage` class exists in `server/storage.ts` for user data
- **CORS**: Custom CORS middleware that allows Replit domains and localhost origins

### Database Schema (Drizzle ORM - PostgreSQL)
- **ORM**: Drizzle ORM with PostgreSQL dialect, configured via `drizzle.config.ts`
- **Schema Location**: `shared/schema.ts` - currently defines a `users` table with id (UUID), username, and password
- **Validation**: drizzle-zod for generating Zod schemas from Drizzle table definitions
- **Migration Output**: `./migrations` directory
- **Current State**: The schema is defined but the app primarily uses in-memory storage. The database connection requires a `DATABASE_URL` environment variable. Run `npm run db:push` to push schema to database

### Ride Logic
- **Distance Calculation**: Haversine formula for calculating distance between coordinates
- **Pricing**: Configurable price per kilometer (default: 1000 IQD/km) and commission rate (default: 10%)
- **Driver Assignment**: Finds the closest available driver to the user's location
- **Coordinates**: Default driver locations are around lat ~33.038, lng ~40.285 (eastern Syria/western Iraq region)

### Build & Deployment
- **Dev Mode**: Two processes run simultaneously - Expo dev server and Express server
- **Production Build**: Custom build script (`scripts/build.js`) handles static web export from Expo, served by Express in production
- **Server Build**: esbuild bundles server code to `server_dist/`
- **Patch Package**: Used via postinstall script for dependency patches

### Key Scripts
- `npm run expo:dev` - Start Expo development server
- `npm run server:dev` - Start Express backend in development
- `npm run db:push` - Push Drizzle schema to PostgreSQL
- `npm run expo:static:build` - Build static web assets
- `npm run server:prod` - Run production server

## External Dependencies

### Core Services
- **PostgreSQL Database**: Required for Drizzle ORM. Needs `DATABASE_URL` environment variable. Currently the app uses in-memory storage but is configured for Postgres migration
- **Replit Environment**: Uses `REPLIT_DEV_DOMAIN`, `REPLIT_DOMAINS`, and `REPLIT_INTERNAL_APP_DOMAIN` for CORS and build configuration

### Key NPM Packages
- **expo** (~54.0.27) - Mobile app framework
- **express** (^5.0.1) - Backend HTTP server
- **drizzle-orm** (^0.39.3) + **drizzle-kit** - Database ORM and migration tooling
- **@tanstack/react-query** (^5.83.0) - Async state management
- **pg** (^8.16.3) - PostgreSQL client for Node.js
- **zod** + **drizzle-zod** - Schema validation
- **react-native-reanimated** (~4.1.1) - Animations
- **expo-location** (~19.0.8) - GPS/location services (available but usage TBD)
- **expo-image-picker** (~17.0.9) - Image selection (available but usage TBD)
- **http-proxy-middleware** (^3.0.5) - Dev server proxying