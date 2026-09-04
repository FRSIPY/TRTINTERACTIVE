# Deployment Guide

## Prerequisites

- Node.js 18+ installed
- npm or yarn package manager
- Vercel account (for Vercel deployment) or other static hosting

## Local Testing

### 1. Environment Setup

```bash
# Install dependencies
npm install

# Set up environment variables
cp .env.example .env.local
# Edit .env.local and set ADMIN_PASSWORD
```

### 2. Development Testing

```bash
# Run development server
npm run dev

# Access at http://localhost:3000
```

### 3. Production Build Test

```bash
# Build for production
npm run build

# Preview production build
npm run preview

# Access at http://localhost:4173
```

## Vercel Deployment

### Method 1: Vercel CLI (Recommended)

```bash
# Install Vercel CLI
npm i -g vercel

# Login to Vercel
vercel login

# Deploy
vercel

# Set environment variable in Vercel dashboard:
# Settings → Environment Variables → Add ADMIN_PASSWORD
```

### Method 2: Vercel Dashboard

1. Push code to GitHub/GitLab/Bitbucket
2. Import project in Vercel dashboard
3. Configure build settings:
   - Framework: Vite
   - Build Command: `npm run build`
   - Output Directory: `dist`
4. Add environment variable: `ADMIN_PASSWORD`
5. Deploy

### Method 3: Vercel JSON Configuration

The project includes `vercel.json` for automatic configuration:

```json
{
  "functions": {
    "api/**/*.js": {
      "runtime": "nodejs18.x"
    }
  },
  "rewrites": [
    {
      "source": "/api/:path*",
      "destination": "/api/:path*"
    },
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```

## Alternative Deployment Platforms

### Netlify

1. **Install Netlify CLI**:
```bash
npm i -g netlify-cli
```

2. **Build and deploy**:
```bash
npm run build
netlify deploy --prod --dir=dist
```

3. **Set environment variable** in Netlify dashboard

### Cloudflare Pages

1. **Build project**:
```bash
npm run build
```

2. **Deploy `dist` folder** via Cloudflare dashboard or Wrangler CLI

3. **Configure Cloudflare Workers** for API functions

### GitHub Pages

1. **Build project**:
```bash
npm run build
```

2. **Push `dist` folder** to GitHub Pages branch

3. **Note**: Serverless functions won't work on GitHub Pages; use alternative hosting for API

## Environment Variables

### Required Variables

- `ADMIN_PASSWORD`: Secure password for admin authentication

### Setting Environment Variables

#### Vercel
- Dashboard: Settings → Environment Variables
- CLI: `vercel env add ADMIN_PASSWORD`

#### Netlify
- Dashboard: Site settings → Environment variables

#### Local Development
- Create `.env.local` file in project root

## Security Verification

### Pre-Deployment Checklist

- [ ] `.env` file is in `.gitignore`
- [ ] No hardcoded passwords in source code
- [ ] ADMIN_PASSWORD is set in production environment
- [ ] API endpoints are protected
- [ ] Public and private data are separated
- [ ] Build succeeds without errors
- [ ] No console.log or debug code in production

### Post-Deployment Testing

1. **Public Access Test**:
   - Navigate to deployed URL
   - Try to evaluate a game without authentication
   - Verify public features work (evaluate, history, compare)

2. **Admin Authentication Test**:
   - Navigate to Admin tab
   - Try to access without password (should fail)
   - Enter correct password (should succeed)
   - Verify protected data operations work

3. **Security Test**:
   - Try to access API endpoints directly without auth (should fail)
   - Verify no passwords appear in client-side JavaScript
   - Check browser console for exposed secrets

## Troubleshooting

### Build Errors

**Issue**: Module not found
```bash
# Solution: Reinstall dependencies
rm -rf node_modules package-lock.json
npm install
```

**Issue**: API functions not working in production
```bash
# Solution: Verify vercel.json configuration
# Ensure API folder is at project root
```

### Authentication Issues

**Issue**: "Server not configured" error
```bash
# Solution: Set ADMIN_PASSWORD environment variable
# Verify variable is set in hosting platform
```

**Issue**: "Invalid credentials" error
```bash
# Solution: Verify password matches exactly
# Check for extra spaces or special characters
```

### Deployment Issues

**Issue**: 404 errors on API routes
```bash
# Solution: Check vercel.json rewrites configuration
# Ensure API functions are properly deployed
```

**Issue**: CORS errors
```bash
# Solution: API functions are same-origin, but if using external APIs,
# add CORS headers in serverless functions
```

## Monitoring and Maintenance

### Recommended Practices

1. **Regular Updates**: Keep dependencies updated
2. **Password Rotation**: Change ADMIN_PASSWORD periodically
3. **Backup Strategy**: Current implementation uses in-memory storage; consider database for production
4. **Access Logs**: Monitor admin access in server logs
5. **Error Monitoring**: Set up error tracking (Sentry, LogRocket)

### Database Migration (Future)

For production use, replace in-memory storage with:

```javascript
// Example: Replace Map with database
import { db } from './database.js';

// Instead of protectedDataStore.get(gameId)
const data = await db.query('SELECT * FROM protected_data WHERE game_id = ?', [gameId]);
```

## Performance Optimization

### Build Optimization

The project uses Vite with default optimizations:
- Code splitting
- Tree shaking
- Minification
- Asset optimization

### API Optimization

- Consider response caching for public endpoints
- Implement rate limiting for admin endpoints
- Add database connection pooling for database-backed implementation

## Compliance and Legal

- Ensure compliance with data protection regulations (GDPR, CCPA)
- Implement data retention policies
- Provide privacy policy for user data
- Secure data transmission (HTTPS enforced by Vercel)

## Support

For deployment issues:
1. Check Vercel deployment logs
2. Review environment variable configuration
3. Verify build output in `dist` folder
4. Test locally with `npm run preview`
