
# Production Setup Guide

## Prerequisites

1. **PostgreSQL Database** (Supabase recommended)
2. **Environment Variables** configured

## Step 1: Create PostgreSQL Database

### Using Supabase (Recommended)

1. Go to [supabase.com](https://supabase.com)
2. Create a new project
3. Navigate to Settings > Database
4. Copy the **Connection String** (URI format)
5. Make sure to select "Transaction" pooling mode for the connection string

### Using Replit PostgreSQL

Alternatively, use Replit's built-in PostgreSQL:
1. Open the Database tab in Replit
2. Click "Create a database"
3. The `DATABASE_URL` will be automatically set

## Step 2: Set Environment Variables

Add these environment variables to your deployment platform:

```bash
DATABASE_URL=postgresql://user:password@host:5432/database
SESSION_SECRET=your-random-secret-key-min-32-chars
NODE_ENV=production
```

### For Vercel:
- Go to Project Settings > Environment Variables
- Add `DATABASE_URL` with your Supabase connection string
- Add `SESSION_SECRET` with a random string (min 32 characters)

### For Replit Deployments:
- Use Secrets tool to add `DATABASE_URL`
- Add `SESSION_SECRET`

## Step 3: Run Database Migrations

After setting up the database, run migrations:

```bash
npx drizzle-kit push
```

This will create all necessary tables in your PostgreSQL database.

## Step 4: Create Admin User

Run the seed script to create your admin user:

```bash
ADMIN_PASSWORD=your-secure-password npm run seed:admin
```

Or create manually via the create-admin script:

```bash
npm run create-admin
```

## Step 5: Deploy

Your application is now ready for production deployment. The app will:
- ✅ Connect to PostgreSQL database
- ✅ Persist all data across deployments
- ✅ Support multiple serverless instances
- ✅ Handle concurrent users properly

## Troubleshooting

### Connection Issues
- Verify `DATABASE_URL` format: `postgresql://user:password@host:port/database`
- Check database allows connections from your deployment platform's IP
- For Supabase: Use the "Transaction" pooler connection string

### Migration Errors
- Ensure database is empty before first migration
- Check PostgreSQL version compatibility (v15+ recommended)

### Session Issues
- Ensure `SESSION_SECRET` is set and at least 32 characters long
- Consider using `connect-pg-simple` for production session storage
