# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Architecture Overview

Hirakhush is a full-stack e-commerce platform with a dockerized microservices architecture:

- **Backend**: Strapi CMS (Node.js/TypeScript) running on port 4000
- **Frontend**: Next.js 14 application (React/TypeScript) running on port 3000
- **Database**: PostgreSQL 16
- **Proxy**: Nginx reverse proxy serving everything on port 80
- **Git Submodules**: Backend and frontend are separate git repositories included as submodules

## Project Structure

The project uses git submodules for backend and frontend:
- `backend/` - Strapi CMS (submodule: git@github.com:Nawel0310/hirakhush-backend.git)
- `frontend/` - Next.js app (submodule: git@github.com:roylischi/hirakhush-frontend.git)
- `nginx/` - Nginx configuration for reverse proxy
- `docker-compose.yml` - Main orchestration file
- `.env` - Environment variables for all services

## Development Commands

### Docker Development (Recommended)
```bash
# Start all services (database, backend, frontend, nginx)
docker-compose up --build

# Start in background
docker-compose up -d --build

# View logs for all services
docker-compose logs -f

# View logs for specific service
docker-compose logs -f frontend
docker-compose logs -f backend

# Stop services
docker-compose down

# Rebuild specific service
docker-compose build frontend
docker-compose build backend

# Development mode (without nginx)
docker-compose up postgres backend frontend
```

### Backend (Strapi) Commands
```bash
cd backend
npm run dev          # Development mode
npm run build        # Build for production
npm run start        # Start production server
npm run seed:example # Seed database with example data
```

### Frontend (Next.js) Commands
```bash
cd frontend
npm run dev    # Development server
npm run build  # Build for production
npm run start  # Start production server
npm run lint   # Run ESLint
```

## Key Technologies

### Backend (Strapi)
- Strapi 5.23.4
- PostgreSQL with pg adapter
- TypeScript support
- Content types: Article, About, Global, Category, Author, Contact
- Admin panel accessible at `/admin`

### Frontend (Next.js)
- Next.js 14 with App Router
- TypeScript
- Tailwind CSS v4
- Radix UI components
- Framer Motion for animations
- React Hook Form with Zod validation
- Shadcn/ui component library

## API Structure

### Strapi Content Types
- **Article**: Blog posts/content articles
- **Category**: Content categorization
- **Author**: Content authors
- **About**: About page content
- **Global**: Site-wide settings
- **Contact**: Contact form submissions

### API Endpoints
- Frontend: `http://localhost/`
- Backend API: `http://localhost/api/`
- Admin Panel: `http://localhost/admin/`
- Health Check: `http://localhost/health`
- Uploads: `http://localhost/uploads/`

## Environment Configuration

Key environment variables in `.env`:
- `NEXT_PUBLIC_STRAPI_URL=http://localhost`
- `DATABASE_*` variables for PostgreSQL connection
- Strapi security keys (APP_KEYS, JWT_SECRET, etc.)
- Email configuration for contact forms

## Nginx Routing

The nginx configuration proxies:
- `/` → Next.js frontend
- `/api/` → Strapi backend API
- `/admin/` → Strapi admin panel
- `/uploads/` → Static file uploads
- All Strapi admin routes (content-manager, content-type-builder, etc.)

## Development Notes

- Always use Docker for consistent development environment
- Backend and frontend are separate repos - use `git submodule update --remote` to sync latest changes
- Frontend uses Tailwind v4 with PostCSS
- UI components follow Shadcn/ui patterns
- Forms use React Hook Form with Zod schema validation
- Database changes require Strapi restart
- File uploads are handled by Strapi and served through nginx

## Testing

No specific test commands found in package.json files. Verify testing setup before implementing tests.

## Common Issues

- If port 80 is in use: `sudo systemctl stop nginx` to stop system nginx
- Database connection issues: Check postgres service with `docker-compose logs postgres`
- Submodule updates: Run `git submodule update --remote` to get latest changes