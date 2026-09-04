# Roblox Game Acquisition Evaluator

A sophisticated web application for evaluating Roblox game acquisitions with secure financial data protection.

## Features

- **Public Scout Access**: Scouts can evaluate games without authentication
- **Secure Financial Data**: Protected acquisition/financial information requires admin authentication
- **Comprehensive Valuation**: Multiple valuation ranges with explainable breakdowns
- **Risk Assessment**: Detailed risk scoring and warnings
- **Comparison Tools**: Compare up to 5 games side-by-side
- **History Tracking**: Save and load evaluations across sessions

## Architecture

### Security Model

- **Public Data**: Game evaluations, valuations, and comparisons are stored client-side in localStorage
- **Protected Data**: Actual purchase prices, development costs, and financial information are stored server-side and require admin authentication
- **Authentication**: Token-based authentication using Bearer tokens with environment variable passwords
- **API Separation**: Public and private data endpoints are completely separated

### Tech Stack

- **Frontend**: React 18 with Vite
- **Backend**: Vercel Serverless Functions (Node.js 18.x)
- **Storage**: localStorage (public data) + server-side in-memory storage (protected data)
- **Deployment**: Optimized for Vercel, compatible with other static hosting platforms

## Environment Variables

### Required for Production

Create a `.env` file (or set in your hosting platform):

```env
ADMIN_PASSWORD=your_secure_admin_password_here
```

**IMPORTANT**: 
- Use a strong, unique password
- Never commit this to version control
- Set this in your hosting platform's environment variables
- Do not share this password publicly

### Local Development

1. Copy `.env.example` to `.env.local`:
```bash
cp .env.example .env.local
```

2. Set your admin password in `.env.local`

## Installation

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## Deployment

### Vercel (Recommended)

1. **Install Vercel CLI** (if not already installed):
```bash
npm i -g vercel
```

2. **Deploy**:
```bash
vercel
```

3. **Set Environment Variable**:
- Go to your Vercel project dashboard
- Navigate to Settings → Environment Variables
- Add `ADMIN_PASSWORD` with your secure password

4. **Redeploy** after setting environment variables

### Manual Deployment

1. **Build the project**:
```bash
npm run build
```

2. **Deploy the `dist` folder** to any static hosting service (Netlify, GitHub Pages, etc.)

3. **For serverless functions**: Deploy the `api` folder to a serverless platform (Vercel, Netlify Functions, AWS Lambda, etc.)

## Security Features

### Protected Data

The following information requires admin authentication:
- Actual purchase price
- Purchase date
- Negotiated purchase price
- Development costs
- Marketing costs
- Other investment costs
- Actual sale price
- Post-acquisition financial results

### Authentication Flow

1. Admin enters password in the Admin tab
2. Frontend sends password as Bearer token to API
3. Server validates against `ADMIN_PASSWORD` environment variable
4. Protected data access is granted only with valid token
5. Password is never stored in client-side code or localStorage

### Data Separation

- **Public**: Game metrics, valuations, scores, comparisons (localStorage)
- **Protected**: Financial acquisition data (server-side storage with authentication)

## API Endpoints

### Public
- `GET /api/public-data` - Public evaluation data (no auth required)

### Protected (Requires Authentication)
- `GET /api/protected-data` - Get all protected financial data
- `GET /api/protected-data?gameId=xxx` - Get specific game's financial data
- `POST /api/protected-data` - Create/update protected financial data
- `DELETE /api/protected-data?gameId=xxx` - Delete protected financial data

## Usage

### For Scouts (Public Access)

1. Navigate to the application
2. Enter game metrics in the Evaluate tab
3. Click "Run Evaluation" to get valuation
4. View results, scores, and recommendations
5. Save evaluations to history for later reference
6. Compare multiple games using the Compare tab

### For Admins (Protected Data)

1. Navigate to the Admin tab
2. Enter admin password
3. Click "Authenticate"
4. View/manage protected financial data
5. Add financial information to history entries
6. Delete sensitive data when needed

## Configuration

Valuation parameters can be adjusted in `src/App.jsx`:

- `CONFIG.BUDGET.MAX_ACQUISITION` - Maximum acquisition budget
- `CONFIG.DEV_RATES` - Development hourly rates
- `CONFIG.DEAL_SCORE_WEIGHTS` - Deal score component weights
- `CONFIG.CCU_VALUE_CURVE` - CCU to value mapping
- `CONFIG.SELLER_MOTIVATION` - Seller motivation multipliers
- `CONFIG.CONTENT_STAGES` - Content depth scoring

## Development

### Project Structure

```
├── api/                    # Serverless functions
│   ├── auth.js            # Authentication logic
│   ├── protected-data.js  # Protected data endpoints
│   └── public-data.js     # Public data endpoints
├── src/
│   ├── App.jsx            # Main React application
│   └── main.jsx           # React entry point
├── index.html             # HTML entry point
├── package.json           # Dependencies
├── vite.config.js         # Vite configuration
├── vercel.json            # Vercel deployment config
└── README.md              # This file
```

### Code Quality

- Modular architecture with clear separation of concerns
- Configurable valuation parameters
- Comprehensive error handling
- Professional UI/UX design
- No hardcoded secrets

## Limitations

- **Serverless Storage**: Current implementation uses in-memory storage (resets on redeploy)
- **Single Browser**: localStorage is per-browser, not synchronized across devices
- **No Database**: For production with persistence, integrate a real database
- **Client-Side Validation**: All validation happens client-side for public data

## Future Enhancements

- Database integration for persistent protected data
- Multi-user support with individual accounts
- Advanced analytics and reporting
- API integration with Roblox for automatic data fetching
- Mobile-responsive design improvements
- TypeScript migration for type safety

## Security Audit Checklist

- ✅ No hardcoded passwords in client code
- ✅ Environment variables for sensitive data
- ✅ Separate public/private data handling
- ✅ Server-side authentication validation
- ✅ Bearer token authentication
- ✅ Protected API endpoints
- ✅ No secrets in git history
- ✅ Proper error handling without information leakage
- ✅ Data separation enforced at API level

## License

Internal tool for Roblox game acquisition evaluation.
