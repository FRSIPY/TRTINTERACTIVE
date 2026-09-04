# Testing Guide

## Pre-Deployment Testing Checklist

### 1. Code Structure Verification

```bash
# Verify project structure
ls -la
# Expected: api/, src/, index.html, package.json, vite.config.js, etc.

# Verify no forbidden files
cat .gitignore
# Ensure .env, .env.local, node_modules/ are ignored
```

### 2. Dependency Installation

```bash
# Install dependencies
npm install

# Verify installation
ls node_modules
# Should include react, react-dom, vite, @vitejs/plugin-react
```

### 3. Development Build Test

```bash
# Start development server
npm run dev

# Manual Testing:
# 1. Navigate to http://localhost:3000
# 2. Test game evaluation (enter sample data)
# 3. Verify results display correctly
# 4. Test history save/load
# 5. Test comparison feature
# 6. Test admin authentication (should fail without password)
```

### 4. Production Build Test

```bash
# Build for production
npm run build

# Verify build output
ls dist/
# Should include index.html, assets/*.js, assets/*.css

# Preview production build
npm run preview

# Test at http://localhost:4173
```

### 5. Security Testing

#### Public Access Test
```bash
# 1. Open application without authentication
# 2. Try to evaluate a game
# 3. Try to access history
# 4. Try to use comparison
# Expected: All public features work without password
```

#### Admin Authentication Test
```bash
# 1. Navigate to Admin tab
# 2. Try to access without password
# Expected: "Authentication failed" error

# 3. Enter correct ADMIN_PASSWORD
# Expected: Authentication succeeds, admin panel opens

# 4. Try to add financial data
# Expected: Data saves successfully

# 5. Logout and try to access admin again
# Expected: Authentication required again
```

#### API Security Test
```bash
# Test without authentication (should fail):
curl -X GET http://localhost:3000/api/protected-data
# Expected: 401 Unauthorized

# Test with wrong password (should fail):
curl -X GET http://localhost:3000/api/protected-data \
  -H "Authorization: Bearer wrong_password"
# Expected: 401 Unauthorized

# Test with correct password (should succeed):
curl -X GET http://localhost:3000/api/protected-data \
  -H "Authorization: Bearer YOUR_ADMIN_PASSWORD"
# Expected: 200 OK with data
```

### 6. Client Bundle Security Check

```bash
# Build production bundle
npm run build

# Search for secrets in bundle
grep -r "ADMIN_PASSWORD" dist/
grep -r "password" dist/
grep -r "secret" dist/
# Expected: No matches (except in comments)

# Check for hardcoded values
grep -r "Bearer" dist/
# Should only appear in API calls, not with actual passwords
```

### 7. Functionality Testing

#### Valuation Engine Test
- Test with 0 CCU → Should produce minimal valuation
- Test with high CCU (500+) → Should produce significant valuation
- Test with declining trend → Should reduce valuation
- Test with high risk → Should reduce valuation
- Test with missing data → Should use reasonable defaults

#### History Persistence Test
- Save evaluation to history
- Refresh page
- Verify history persists
- Clear history
- Verify history is cleared

#### Comparison Test
- Add 3 games to comparison
- Verify all metrics display
- Remove one game
- Verify comparison updates
- Try to add 6th game
- Should be blocked (max 5)

#### Admin Functions Test
- Authenticate as admin
- Add financial data to a game
- Verify data appears in admin panel
- Delete financial data
- Verify data is removed
- Logout
- Try to access financial data
- Should be inaccessible

### 8. Cross-Browser Testing

Test in multiple browsers:
- Chrome/Edge (Chromium)
- Firefox
- Safari (if available)

Verify:
- UI renders correctly
- Interactions work
- localStorage functions
- API calls succeed

### 9. Responsive Design Test

Test at different screen sizes:
- Desktop (1920x1080)
- Laptop (1366x768)
- Tablet (768x1024)
- Mobile (375x667)

Verify:
- Layout adapts appropriately
- Forms remain usable
- No horizontal scrolling

### 10. Error Handling Test

- Test with invalid numbers in fields
- Test with extremely large numbers
- Test with negative numbers where inappropriate
- Test with network disconnected
- Verify graceful error handling

## Sample Test Data

### High-Value Game
```
Game Name: Successful Simulator
Current CCU: 150
Average CCU: 120
Peak CCU: 300
CCU Trend: Growing
Total Visits: 5000000
Monthly Revenue: 15000
Seller Asking Price: 80000
```

### Low-Value Game
```
Game Name: New Obby
Current CCU: 5
Average CCU: 3
Peak CCU: 15
CCU Trend: Declining
Total Visits: 10000
Monthly Revenue: 100
Seller Asking Price: 5000
```

### Risky Game
```
Game Name: Broken Game
Current CCU: 50
Average CCU: 40
Fake Traffic Suspicion: true
Copyright Concerns: true
Broken Systems: 3
Bugs Level: 80
```

## Performance Testing

### Load Time Test
```bash
# Build production bundle
npm run build

# Check bundle size
du -sh dist/
# Should be < 500KB for reasonable performance

# Test load time with network throttling
# (Chrome DevTools → Network → Throttling)
```

### API Response Time Test
```bash
# Test API response times
time curl -X GET http://localhost:3000/api/public-data
# Should be < 100ms for local, < 500ms for production
```

## Accessibility Testing

- Test keyboard navigation
- Test screen reader compatibility
- Verify color contrast ratios
- Test form labels and hints

## Deployment Readiness Checklist

- [ ] All tests pass
- [ ] No console errors in production build
- [ ] Environment variables configured
- [ ] Security audit passed
- [ ] Performance acceptable
- [ ] Cross-browser compatibility verified
- [ ] Mobile responsiveness confirmed
- [ ] Documentation complete
- [ ] Backup/recovery plan in place
- [ ] Monitoring configured

## Post-Deployment Monitoring

After deployment, monitor:
- Error rates in server logs
- API response times
- User authentication patterns
- Failed authentication attempts
- Storage usage patterns
- Browser compatibility issues

## Rollback Plan

If issues occur after deployment:
1. Revert to previous deployment
2. Check server logs for errors
3. Test fixes locally
4. Re-deploy after verification
5. Monitor closely after re-deployment
