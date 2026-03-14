# FinPilot - AI-Powered Personal Finance Assistant

## Overview
FinPilot is a comprehensive web application designed to help users manage their personal finances with AI-driven insights, expense tracking, and financial planning tools.

## Features

### 1. User Authentication
- Secure signup/login with JWT tokens
- Password hashing with bcryptjs
- 7-day token expiration

### 2. Transaction Management
- Manual transaction entry
- CSV file upload for bulk transactions
- Auto-categorization using merchant names
- Support for income and expense tracking

### 3. AI Expense Categorization
- Automatic categorization based on merchant keywords
- 9 categories: Food, Transport, Shopping, Bills, Entertainment, Healthcare, Education, Investments, Personal
- Extensible keyword matching system

### 4. Dashboard
- Real-time spending overview
- Monthly income vs expenses
- Savings percentage calculation
- Category-wise spending breakdown (pie chart)
- Financial health score

### 5. Suspicious Spending Detection
- Anomaly detection using statistical analysis
- Flags unusually high transactions (2+ std dev above mean)
- Detects repeated transactions in short timeframe
- Customizable thresholds

### 6. AI Financial Advisor
- Chat-based financial advice using OpenAI GPT-3.5
- Personalized recommendations based on spending patterns
- Can answer questions like "Can I afford a $1000 purchase?"
- Considers user's financial summary in responses

### 7. Retirement Planner
- Calculate projected retirement savings
- Assumes 7% annual return (conservative)
- Inputs: current age, retirement age, monthly savings
- Shows total projected savings at retirement

### 8. Financial Insights
- 6-month spending trends visualization
- Category breakdown by month
- Trend analysis with charts
- Year-over-year comparisons

### 9. Financial Health Score
- Calculated based on: savings rate, expense ratio, income stability
- Scale: 0-100
- Categories: Excellent (80+), Good (60-79), Fair (40-59), Poor (<40)

## Tech Stack

### Frontend
- **Framework**: Next.js 14
- **Styling**: TailwindCSS
- **Charts**: Recharts
- **HTTP Client**: Axios
- **State Management**: React Hooks
- **Icons**: Lucide React

### Backend
- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: PostgreSQL
- **Authentication**: JWT (jsonwebtoken)
- **Password Hashing**: bcryptjs
- **File Upload**: Multer
- **CSV Processing**: csvtojson
- **AI Integration**: OpenAI API

## Database Schema

### Users Table
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password VARCHAR(255) NOT NULL,
  name VARCHAR(255),
  monthly_income DECIMAL(12, 2) DEFAULT 0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Transactions Table
```sql
CREATE TABLE transactions (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  date DATE NOT NULL,
  description VARCHAR(500),
  category VARCHAR(100),
  amount DECIMAL(12, 2) NOT NULL,
  type VARCHAR(20) NOT NULL, -- 'income' or 'expense'
  merchant_name VARCHAR(255),
  is_suspicious BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Financial Goals Table
```sql
CREATE TABLE financial_goals (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  goal_type VARCHAR(100), -- 'retirement', 'savings', etc.
  current_age INTEGER,
  retirement_age INTEGER,
  monthly_savings DECIMAL(12, 2),
  current_savings DECIMAL(12, 2),
  target_amount DECIMAL(12, 2),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Financial Health Scores Table
```sql
CREATE TABLE financial_health_scores (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  score DECIMAL(5, 2),
  savings_rate DECIMAL(5, 2),
  expense_ratio DECIMAL(5, 2),
  income_stability DECIMAL(5, 2),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## API Endpoints

### Authentication
- `POST /api/auth/signup` - Register new user
- `POST /api/auth/login` - Login user
- `GET /api/auth/profile` - Get user profile (requires auth)
- `PUT /api/auth/profile` - Update user profile (requires auth)

### Transactions
- `POST /api/transactions` - Add transaction (requires auth)
- `GET /api/transactions` - Get all transactions with filters (requires auth)
- `POST /api/transactions/upload` - Upload CSV file (requires auth)
- `POST /api/transactions/check-suspicious` - Check for anomalies (requires auth)
- `DELETE /api/transactions/:id` - Delete transaction (requires auth)

### Insights
- `GET /api/insights/monthly` - Get monthly spending summary (requires auth)
- `GET /api/insights/health-score` - Get financial health score (requires auth)
- `GET /api/insights/spending-trends` - Get 6-month trends (requires auth)

### Advisor
- `POST /api/advisor/advice` - Get AI financial advice (requires auth)
- `POST /api/advisor/retirement-plan` - Calculate retirement savings (requires auth)

## Installation & Setup

### Backend Setup

1. Clone the repository
```bash
cd finpilot/backend
npm install
```

2. Create `.env` file from `.env.example`
```bash
cp .env.example .env
```

3. Configure environment variables:
```
DATABASE_URL=postgresql://user:password@localhost:5432/finpilot
JWT_SECRET=your_secure_random_string_here
OPENAI_API_KEY=your_openai_api_key
PORT=5000
FRONTEND_URL=http://localhost:3000
NODE_ENV=development
```

4. Set up PostgreSQL database:
```bash
createdb finpilot
```

5. Start the backend:
```bash
npm run dev
```

### Frontend Setup

1. Navigate to frontend directory
```bash
cd finpilot/frontend
npm install
```

2. Create `.env.local` file:
```
NEXT_PUBLIC_API_URL=http://localhost:5000/api
```

3. Start the frontend:
```bash
npm run dev
```

Visit `http://localhost:3000` in your browser.

## Project Structure

```
finpilot/
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   └── database.js
│   │   ├── routes/
│   │   │   ├── auth.js
│   │   │   ├── transactions.js
│   │   │   ├── advisor.js
│   │   │   └── insights.js
│   │   ├── controllers/
│   │   ├── middleware/
│   │   │   └── auth.js
│   │   ├── utils/
│   │   │   ├── categories.js
│   │   │   └── anomalyDetection.js
│   │   ├── ai/
│   │   │   └── categorizer.js
│   │   └── index.js
│   ├── package.json
│   └── .env.example
│
├── frontend/
│   ├── app/
│   │   ├── page.js (home)
│   │   ├── layout.js
│   │   ├── login/
│   │   ├── signup/
│   │   ├── dashboard/
│   │   ├── transactions/
│   │   ├── insights/
│   │   ├── advisor/
│   │   ├── retirement/
│   │   └── settings/
│   ├── components/
│   ├── lib/
│   │   └── api.js
│   ├── styles/
│   │   └── globals.css
│   ├── package.json
│   ├── next.config.js
│   └── tailwind.config.js
│
└── README.md
```

## Sample CSV Format for Transaction Upload

```csv
date,description,merchant_name,amount,type
2024-01-15,Lunch,Starbucks,5.50,expense
2024-01-16,Gas,Shell Gas Station,45.00,expense
2024-01-17,Salary,Employer,3000.00,income
2024-01-18,Groceries,Whole Foods,75.30,expense
```

## Example API Requests

### Sign Up
```bash
curl -X POST http://localhost:5000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "secure_password",
    "name": "John Doe"
  }'
```

### Add Transaction
```bash
curl -X POST http://localhost:5000/api/transactions \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "date": "2024-01-20",
    "description": "Coffee",
    "merchant_name": "Starbucks",
    "amount": 5.50,
    "type": "expense"
  }'
```

### Get Monthly Insights
```bash
curl -X GET "http://localhost:5000/api/insights/monthly?year=2024&month=1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get AI Advice
```bash
curl -X POST http://localhost:5000/api/advisor/advice \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "question": "How can I reduce my food spending?"
  }'
```

## Features Roadmap

- [ ] Mobile app (React Native)
- [ ] Bill reminders and notifications
- [ ] Budget alerts and thresholds
- [ ] Goal tracking and progress
- [ ] Investment portfolio tracking
- [ ] Tax optimization suggestions
- [ ] Multi-currency support
- [ ] Bank account integration (Plaid)
- [ ] Social sharing and comparison (anonymized)
- [ ] Advanced forecasting models

## Security Considerations

1. **JWT Tokens**: 7-day expiration for tokens
2. **Password Hashing**: bcryptjs with salt rounds
3. **CORS**: Configured for specific frontend URL
4. **SQL Injection**: Using parameterized queries
5. **Rate Limiting**: Recommended to add middleware
6. **HTTPS**: Enable in production
7. **Env Variables**: Never commit .env files

## Performance Optimization

- Database indexes on frequently queried columns
- Lazy loading of components
- Chart libraries optimized for large datasets
- Request caching with cookies
- Efficient category keyword matching

## Support & Contributing

For issues or feature requests, please open an issue on the GitHub repository.

## License

MIT License - Feel free to use this project for personal or commercial purposes.
