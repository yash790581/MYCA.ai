# FinPilot - Quick Start (5 minutes)

## Requirements
- Node.js v16+
- PostgreSQL
- OpenAI API key (free tier available)

## Quick Setup

### 1. Database (2 min)
```bash
# Create database
createdb finpilot
```

### 2. Backend (1.5 min)
```bash
cd fintech_project/finpilot/backend
npm install

# Create .env
cat > .env << EOF
DATABASE_URL=postgresql://postgres@localhost:5432/finpilot
JWT_SECRET=your_random_secret_key_here
OPENAI_API_KEY=your_openai_key_here
PORT=5000
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
EOF

npm run dev
```

### 3. Frontend (1.5 min)
In a NEW terminal:
```bash
cd fintech_project/finpilot/frontend
npm install
npm run dev
```

### 4. Test It
- Open http://localhost:3000
- Sign up with test@example.com / password123
- Go to Settings, set Monthly Income = 4500
- Upload sample_transactions.csv
- View Dashboard!

## API Quick Reference

```bash
# Sign Up
curl -X POST http://localhost:5000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123","name":"Test User"}'

# Get Token
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'

# Add Transaction
curl -X POST http://localhost:5000/api/transactions \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"date":"2024-01-15","merchant_name":"Starbucks","amount":5.50,"type":"expense"}'

# Get Dashboard Data
curl -X GET "http://localhost:5000/api/insights/monthly?year=2024&month=1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Key Files

| File | Purpose |
|------|---------|
| `backend/src/index.js` | Express server setup |
| `backend/src/routes/auth.js` | Login/Signup |
| `backend/src/routes/transactions.js` | Transaction management |
| `backend/src/routes/advisor.js` | AI advice & retirement |
| `backend/src/routes/insights.js` | Dashboard data |
| `frontend/app/dashboard/page.js` | Main dashboard |
| `frontend/app/advisor/page.js` | AI advisor page |
| `frontend/lib/api.js` | API client config |

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Connection refused | Start PostgreSQL: `pg_ctl -D /usr/local/var/postgres start` |
| Port 5000 in use | Change PORT in .env |
| "Cannot find module" | Run `npm install` again |
| "Invalid token" | Clear cookies and log in again |
| OpenAI errors | Check API key is valid and has credits |

## Features Overview

✅ User Authentication (JWT)
✅ Transaction Management (Manual + CSV)
✅ Auto-categorization
✅ Dashboard with Charts
✅ AI Financial Advisor
✅ Retirement Calculator
✅ Health Score
✅ Spending Trends
✅ Anomaly Detection
✅ Responsive Design

## Database Auto-Setup

Tables are created automatically on first backend start:
- `users` - User accounts
- `transactions` - Income/expense entries
- `financial_goals` - Retirement/savings goals
- `financial_health_scores` - Health metrics

## CSV Format for Bulk Upload

```csv
date,description,merchant_name,amount,type
2024-01-15,Coffee,Starbucks,5.50,expense
2024-01-16,Salary,Employer,4500.00,income
```

File is at: `fintech_project/finpilot/sample_transactions.csv`

## Docs

- Full setup: See `SETUP.md`
- Full documentation: See `README.md`
- API details: See `README.md` API section
- Database schema: See `README.md` Database section

## Common Commands

```bash
# Start backend with auto-reload
npm run dev

# Start frontend
npm run dev

# Build for production
npm run build

# Start production server
npm start
```

## Environment Variables Needed

```
# Backend (.env)
DATABASE_URL=postgresql://...
JWT_SECRET=long_random_string
OPENAI_API_KEY=sk-...
PORT=5000
FRONTEND_URL=http://localhost:3000

# Frontend (.env.local)
NEXT_PUBLIC_API_URL=http://localhost:5000/api
```

## Performance Tips

1. Add indexes to large tables
2. Cache API responses
3. Use pagination for transactions
4. Lazy load charts
5. Enable gzip compression

## Security Notes

- Never commit .env files
- Use HTTPS in production
- Rotate JWT_SECRET periodically
- Add rate limiting middleware
- Validate all user inputs

## What's Working

✅ User signup/login with JWT
✅ Transaction CRUD operations
✅ CSV file uploads
✅ Auto-categorization
✅ Dashboard calculations
✅ Health score computation
✅ AI advisor with GPT-3.5
✅ Retirement calculator
✅ Spending trends analysis
✅ Responsive TailwindCSS design
✅ Mobile friendly UI

## What's Coming Soon

- [ ] Bank account integration (Plaid)
- [ ] Mobile app (React Native)
- [ ] Budget alerts
- [ ] Bill reminders
- [ ] Investment tracking
- [ ] Tax optimization

Enjoy FinPilot! 🚀
