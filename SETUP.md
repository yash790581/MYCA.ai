# FinPilot Setup Guide

## Prerequisites

Before getting started, ensure you have the following installed:
- **Node.js** (v16+)
- **npm** or **yarn**
- **PostgreSQL** (v12+)
- **Git** (optional, for version control)

## Step-by-Step Installation

### 1. PostgreSQL Database Setup

#### On Windows:
1. Download PostgreSQL from https://www.postgresql.org/download/windows/
2. Run the installer and follow the setup wizard
3. Remember the password you set for the `postgres` user
4. Make sure PostgreSQL service is running

#### On macOS:
```bash
brew install postgresql@14
brew services start postgresql@14
```

#### On Linux:
```bash
sudo apt-get install postgresql postgresql-contrib
sudo systemctl start postgresql
```

#### Create Database:
```bash
# Connect to PostgreSQL
psql -U postgres

# In psql terminal:
CREATE DATABASE finpilot;
\q
```

### 2. Backend Setup

```bash
# Navigate to backend directory
cd fintech_project/finpilot/backend

# Install dependencies
npm install

# Create .env file from example
cp .env.example .env
```

#### Configure .env file:
```
# Database connection
DATABASE_URL=postgresql://postgres:your_password@localhost:5432/finpilot

# JWT Configuration
JWT_SECRET=your_super_secret_random_string_min_32_chars_long
JWT_EXPIRY=7d

# OpenAI API
OPENAI_API_KEY=your_openai_api_key_from_platform.openai.com

# Server Configuration
PORT=5000
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
```

#### Get OpenAI API Key:
1. Visit https://platform.openai.com
2. Sign up or log in
3. Go to API keys section
4. Create a new secret key
5. Copy and paste it in .env

#### Start Backend Server:
```bash
npm run dev
# Server will run at http://localhost:5000
```

### 3. Frontend Setup

Open a new terminal window:

```bash
# Navigate to frontend directory
cd fintech_project/finpilot/frontend

# Install dependencies
npm install

# Create .env.local file
echo "NEXT_PUBLIC_API_URL=http://localhost:5000/api" > .env.local
```

#### Start Frontend Server:
```bash
npm run dev
# Frontend will run at http://localhost:3000
```

## Testing the Application

### 1. Access the Application
Open your browser and go to: **http://localhost:3000**

### 2. Create Test Account
- Click "Sign Up"
- Enter email: `test@example.com`
- Password: `password123`
- Name: `Test User`
- Click "Sign Up"

### 3. Complete Profile Setup
- Go to **Settings**
- Set **Monthly Income** to `4500`
- Click "Save Changes"

### 4. Add Sample Transactions
#### Option A: Manual Entry
- Go to **Transactions**
- Click "Add Transaction"
- Fill in details:
  - Date: Today
  - Merchant: "Starbucks"
  - Amount: 5.50
  - Type: Expense
- Click "Save"

#### Option B: Upload CSV
- Go to **Transactions**
- Click "Upload CSV"
- Download `sample_transactions.csv` from the project root
- Select it and upload
- 30 sample transactions will be imported

### 5. View Dashboard
- Click **Dashboard** to see:
  - Monthly income and expenses
  - Savings percentage
  - Financial health score
  - Category breakdown pie chart

### 6. Test AI Features
- Go to **AI Advisor**
- Ask: "How can I reduce my food spending?"
- Watch the AI analyze your spending and provide suggestions

### 7. Retirement Planner
- Go to **Retirement**
- Enter:
  - Current Age: 35
  - Retirement Age: 65
  - Current Savings: $50,000
  - Monthly Savings: $500
- Click "Calculate"
- See projected retirement savings

## Troubleshooting

### Backend Issues

**Error: "Cannot find module 'pg'"**
```bash
cd backend
npm install pg
```

**Error: "ECONNREFUSED" - Database connection failed**
- Check PostgreSQL is running
- Verify DATABASE_URL in .env is correct
- Make sure database `finpilot` exists

**Error: "OpenAI API key not configured"**
- Get API key from https://platform.openai.com
- Add it to .env file: `OPENAI_API_KEY=sk-...`

**Port 5000 already in use**
```bash
# Change PORT in .env to 5001 (or any available port)
# Update FRONTEND .env: NEXT_PUBLIC_API_URL=http://localhost:5001/api
```

### Frontend Issues

**Error: "Cannot find module 'recharts'"**
```bash
cd frontend
npm install recharts
```

**Blank page or "Cannot reach server"**
- Check if backend is running (should see logs on port 5000)
- Verify NEXT_PUBLIC_API_URL in .env.local is correct

**Port 3000 already in use**
```bash
# Run on different port
npm run dev -- -p 3001
```

### Authentication Issues

**Getting "Invalid token" errors**
- Clear browser cookies/localStorage
- Go to http://localhost:3000
- Log out and log in again

**Can't log in**
- Check email and password are correct
- Ensure backend database has the user (check PostgreSQL)

## Database Inspection

### View Database Tables:
```bash
psql -U postgres finpilot

# In psql:
\dt                    # List all tables
SELECT * FROM users;   # View users
SELECT * FROM transactions;  # View transactions
\q                     # Exit
```

### Load Sample Data:
```bash
psql -U postgres finpilot < dummy_data.sql
```

## Deployment

### Deploy Backend (Heroku)
```bash
# Install Heroku CLI
npm install -g heroku

# Login to Heroku
heroku login

# Create app
heroku create finpilot-api

# Add PostgreSQL
heroku addons:create heroku-postgresql:hobby-dev

# Set environment variables
heroku config:set JWT_SECRET=your_secret_key
heroku config:set OPENAI_API_KEY=your_api_key

# Deploy
git push heroku main
```

### Deploy Frontend (Vercel)
```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
cd frontend
vercel

# Follow prompts and set:
# NEXT_PUBLIC_API_URL=https://finpilot-api.herokuapp.com/api
```

## Project Structure Overview

```
finpilot/
├── backend/
│   ├── src/
│   │   ├── config/database.js        # DB configuration
│   │   ├── routes/                   # API endpoints
│   │   ├── middleware/auth.js        # JWT authentication
│   │   ├── utils/                    # Helper functions
│   │   ├── ai/categorizer.js        # AI categorization
│   │   └── index.js                  # Express app setup
│   ├── package.json
│   ├── .env.example
│   └── dummy_data.sql                # Sample data
│
├── frontend/
│   ├── app/
│   │   ├── page.js                   # Home page
│   │   ├── layout.js                 # Sidebar layout
│   │   ├── dashboard/page.js         # Dashboard
│   │   ├── transactions/page.js      # Transactions
│   │   ├── advisor/page.js          # AI advisor
│   │   ├── insights/page.js         # Insights
│   │   ├── retirement/page.js       # Retirement planner
│   │   ├── settings/page.js         # Settings
│   │   ├── login/page.js            # Login
│   │   └── signup/page.js           # Signup
│   ├── lib/api.js                    # Axios config
│   ├── styles/globals.css            # TailwindCSS
│   ├── tailwind.config.js
│   ├── next.config.js
│   ├── package.json
│   └── .env.local
│
├── README.md                          # Full documentation
├── SETUP.md                          # This file
└── sample_transactions.csv           # Sample data
```

## Key Features Walkthrough

### 1. Dashboard
- **Monthly Income**: Based on profile setting
- **Total Expenses**: Sum of all expense transactions
- **Savings Rate**: % of income not spent
- **Health Score**: 0-100 based on spending patterns
- **Category Chart**: Visual breakdown of spending

### 2. Transactions
- **Add Manually**: Click "Add Transaction"
- **Upload CSV**: Format: date, description, merchant, amount, type
- **Auto-categorize**: System auto-categorizes based on merchant name
- **Delete**: Remove transactions with delete button
- **Filter**: Filter by date, category, or type (query parameters)

### 3. AI Advisor
- Ask any financial question
- AI analyzes your spending data
- Provides personalized advice
- Example questions:
  - "Can I afford a $1000 purchase?"
  - "How can I save more?"
  - "What's my biggest expense?"

### 4. Retirement Planner
- Calculate retirement savings
- Based on 7% annual return assumption
- Shows projection with compound interest
- Helps plan for long-term goals

### 5. Insights & Trends
- 6-month spending trends
- Category breakdown by month
- Visualize spending patterns
- Identify areas to cut

## Tips for Best Results

1. **Set Monthly Income**: Accurate calculations depend on this
2. **Use Descriptive Merchant Names**: Better categorization
3. **Upload Historical Data**: More data = better AI insights
4. **Regular Monitoring**: Check dashboard weekly
5. **Review Recommendations**: AI advisor learns from your behavior

## Next Steps

After setup, you can:
1. Explore all features with sample data
2. Add your real transactions
3. Connect with your bank (future feature)
4. Set savings goals
5. Get personalized financial advice

## Support

- Check logs: Backend logs print to terminal
- Use browser DevTools for frontend issues
- Check PostgreSQL logs for database issues

Happy financial tracking! 🎉
