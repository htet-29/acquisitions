# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Development Commands

**Run development server:**

```bash
npm run dev
```

Starts the server with Node's `--watch` flag for auto-reload on file changes.

**Linting:**

```bash
npm run lint          # Check for linting errors
npm run lint:fix      # Auto-fix linting errors
```

**Formatting:**

```bash
npm run format        # Format all files with Prettier
npm run format:check  # Check if files are formatted correctly
```

**Database operations:**

```bash
npm run db:generate   # Generate Drizzle migrations from schema
npm run db:migrate    # Apply migrations to database
npm run db:studio     # Open Drizzle Studio (visual database browser)
```

## High-Level Architecture

**Overview:**

- Node.js Express API with ES modules for acquisitions management
- Neon serverless PostgreSQL database with Drizzle ORM
- Authentication system with JWT tokens stored in httpOnly cookies

**Application Entry Chain:**

- `src/index.js` → loads environment variables and imports `server.js`
- `src/server.js` → starts Express server on specified PORT
- `src/app.js` → defines Express app with middleware and routes

**Path Aliases:**
The project uses import path aliases (defined in `package.json`):

- `#config/*` → `./src/config/*`
- `#controllers/*` → `./src/controllers/*`
- `#middleware/*` → `./src/middleware/*`
- `#models/*` → `./src/models/*`
- `#routes/*` → `./src/routes/*`
- `#services/*` → `./src/services/*`
- `#utils/*` → `./src/utils/*`
- `#validations/*` → `./src/validations/*`

**Request Flow (MVC-like pattern):**

1. **Routes** (`src/routes/`) - Define API endpoints
2. **Controllers** (`src/controllers/`) - Handle HTTP requests/responses
3. **Validations** (`src/validations/`) - Validate input with Zod schemas
4. **Services** (`src/services/`) - Business logic layer
5. **Models** (`src/models/`) - Drizzle ORM schema definitions

**Authentication Flow Example (sign-up):**

1. Request → `auth.routes.js` (`POST /api/auth/sign-up`)
2. Controller → `auth.controller.js` validates with Zod `signUpSchema`
3. Service → `auth.service.js` hashes password and creates user
4. Model → `user.model.js` defines the users table schema
5. Response → JWT token set in httpOnly cookie, user data returned

**Logging:**

- Winston logger configured in `src/config/logger.js`
- Logs written to `logs/error.log` (errors only) and `logs/combined.log` (all logs)
- Console output enabled in non-production environments
- Morgan middleware logs HTTP requests through Winston

**Database:**

- Neon serverless PostgreSQL accessed via `@neondatabase/serverless`
- Drizzle ORM instance exported from `src/config/database.js` as `db`
- Schemas defined in `src/models/*.js` using Drizzle's `pgTable`
- Migrations stored in `drizzle/` directory

**Utilities:**

- `src/utils/jwt.js` - JWT signing/verification with 1-day expiration
- `src/utils/cookies.js` - Cookie management with security defaults (httpOnly, sameSite, secure in prod)
- `src/utils/format.js` - Error formatting helpers (e.g., Zod validation errors)

## Key Configuration

**ES Modules:**

- Project uses `"type": "module"` in `package.json`
- All imports use `.js` extensions

**Environment Variables:**
Required variables (see `.env.example`):

- `PORT` - Server port (default: 3000)
- `NODE_ENV` - Environment (development/production)
- `LOG_LEVEL` - Winston log level (default: info)
- `DATABASE_URL` - Neon PostgreSQL connection string
- `JWT_SECRET` - Secret key for JWT signing (falls back to default in code, but should be set)

**Database Configuration:**

- Drizzle config in `drizzle.config.js`
- Schema files: `src/models/*.js`
- Output directory: `./drizzle`
- Dialect: PostgreSQL

**Code Style:**

- ESLint rules: 2-space indentation, single quotes, semicolons required
- Prettier: 80-character line width, no trailing commas in ES5, LF line endings
- Unused variables allowed if prefixed with underscore (e.g., `_req`)
