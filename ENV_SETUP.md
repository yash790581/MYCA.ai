# Generate random secrets for .env

## On macOS/Linux:
```bash
# Generate JWT Secret
openssl rand -base64 32

# Generate a secure password
openssl rand -hex 16
```

## On Windows (PowerShell):
```powershell
# Generate random string
[System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes((Get-Random -Maximum 999999999).ToString())).Substring(0,32)
```

## Backend .env File Example

```env
# PostgreSQL Database Connection
DATABASE_URL=postgresql://postgres:your_password@localhost:5432/finpilot

# JWT Configuration
JWT_SECRET=your_random_secret_here_min_32_chars_for_security
JWT_EXPIRY=7d

# OpenAI API (Get from https://platform.openai.com/api-keys)
OPENAI_API_KEY=sk-your_api_key_here

# Server Configuration
PORT=5000
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
```

### How to get each value:

#### DATABASE_URL
1. Install PostgreSQL
2. Create database: `createdb finpilot`
3. Default connection: `postgresql://postgres@localhost:5432/finpilot`
4. Or with password: `postgresql://postgres:password@localhost:5432/finpilot`

#### JWT_SECRET
Generate 32+ character random string:
```bash
# Linux/Mac
openssl rand -base64 32

# Windows PowerShell
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes([Guid]::NewGuid().ToString()))
```

#### OPENAI_API_KEY
1. Go to https://platform.openai.com
2. Sign in or create account
3. Click API keys on left sidebar
4. Click "Create new secret key"
5. Copy the key (it starts with `sk-`)
6. Paste into .env

## Frontend .env.local File

```env
# API Backend URL
NEXT_PUBLIC_API_URL=http://localhost:5000/api

# For production, use your deployed backend URL:
# NEXT_PUBLIC_API_URL=https://your-api.herokuapp.com/api
```

## Testing with Different Environments

### Development .env
```env
DATABASE_URL=postgresql://postgres@localhost:5432/finpilot
JWT_SECRET=dev_secret_key_change_in_production
OPENAI_API_KEY=sk-...
PORT=5000
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
```

### Production .env
```env
DATABASE_URL=postgresql://user:password@prod-db-host:5432/finpilot
JWT_SECRET=production_secret_key_very_long_and_random
OPENAI_API_KEY=sk-...
PORT=5000
NODE_ENV=production
FRONTEND_URL=https://yourdomain.com
```

## Security Checklist

- [ ] JWT_SECRET is at least 32 characters
- [ ] DATABASE_URL has secure password
- [ ] OPENAI_API_KEY is kept secret
- [ ] .env files are in .gitignore
- [ ] NODE_ENV is set correctly
- [ ] FRONTEND_URL matches actual frontend domain
- [ ] Never commit .env to git
- [ ] Rotate secrets periodically

## Troubleshooting

### "Cannot connect to database"
- Check PostgreSQL is running: `psql -U postgres`
- Verify database exists: `createdb finpilot`
- Check DATABASE_URL format
- Check password is correct

### "Invalid OpenAI API key"
- Check key starts with `sk-`
- Verify key is active (not revoked)
- Check you have credits in OpenAI account
- Make sure no extra spaces in .env

### "JWT token invalid"
- Ensure JWT_SECRET is the same in both sessions
- Check JWT_EXPIRY format (e.g., "7d")
- Clear old tokens from browser

## Example .env Setup Commands

### Linux/Mac:
```bash
cd backend
cp .env.example .env

# Edit .env with your values
nano .env

# Or use environment substitution
cat > .env << EOF
DATABASE_URL=postgresql://postgres@localhost:5432/finpilot
JWT_SECRET=$(openssl rand -base64 32)
OPENAI_API_KEY=sk-your_key_here
PORT=5000
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
EOF
```

### Windows PowerShell:
```powershell
cd backend
Copy-Item .env.example .env

# Edit .env with text editor or:
$secret = [Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes((Get-Random -Maximum 999999999).ToString() + (Get-Random -Maximum 999999999).ToString()))
@"
DATABASE_URL=postgresql://postgres@localhost:5432/finpilot
JWT_SECRET=$secret
OPENAI_API_KEY=sk-your_key_here
PORT=5000
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
"@ | Out-File .env
```

## Production Deployment Notes

For Heroku/Vercel deployment:

1. **Backend (Heroku)**:
   - Set config vars via dashboard or CLI:
   ```bash
   heroku config:set JWT_SECRET="your_prod_secret"
   heroku config:set OPENAI_API_KEY="sk-..."
   heroku addons:create heroku-postgresql:hobby-dev
   ```

2. **Frontend (Vercel)**:
   - Set environment variables in Vercel dashboard
   - `NEXT_PUBLIC_API_URL=https://your-api.herokuapp.com/api`

3. **Database**:
   - Use managed PostgreSQL (Heroku, AWS RDS, Supabase, etc.)
   - Ensure SSL connections

## Keep Your Secrets Safe! 🔐

Remember:
- ✅ Keep .env in .gitignore
- ✅ Use strong, random values
- ✅ Rotate secrets after breaches
- ✅ Never share secrets in chat/email
- ✅ Use separate secrets for dev/prod
- ✅ Enable secret rotation in production
