# ⚡ OAuth Quick Start

Fast setup for Google and GitHub authentication.

---

## 🚀 Quick Setup (5 Minutes)

### Step 1: Install Dependencies
```bash
cd research_assistant
pip install django-allauth==0.57.0 dj-rest-auth==5.0.2
```

### Step 2: Run Migrations
```bash
python manage.py migrate
```

### Step 3: Get OAuth Credentials

#### Google:
1. Go to: https://console.cloud.google.com/
2. Create project → Enable Google+ API
3. Create OAuth credentials
4. Add redirect URI: `http://127.0.0.1:8000/accounts/google/login/callback/`
5. Copy Client ID and Secret

#### GitHub:
1. Go to: https://github.com/settings/developers
2. New OAuth App
3. Add callback URL: `http://127.0.0.1:8000/accounts/github/login/callback/`
4. Copy Client ID and Secret

### Step 4: Add to .env

Create/update `.env` in `research_assistant/`:

```env
# Database (existing)
DB_NAME=research_assistant_db
DB_USER=root
DB_PASSWORD=your_password
DB_HOST=localhost
DB_PORT=3306

# Google OAuth (NEW)
GOOGLE_CLIENT_ID=your_google_client_id.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-your_google_secret

# GitHub OAuth (NEW)
GITHUB_CLIENT_ID=Iv1.your_github_client_id
GITHUB_CLIENT_SECRET=your_github_secret
```

### Step 5: Configure Django Site

```bash
python manage.py shell
```

```python
from django.contrib.sites.models import Site
site = Site.objects.get(id=1)
site.domain = '127.0.0.1:8000'
site.name = 'Research Assistant'
site.save()
exit()
```

### Step 6: Start Servers

```bash
# Terminal 1 - Backend
cd research_assistant
python manage.py runserver

# Terminal 2 - Frontend
cd research-assistant-frontend
npm run dev
```

### Step 7: Test

1. Open: http://localhost:5173/login
2. Click "Continue with Google" or "Continue with GitHub"
3. Authenticate
4. You're in! 🎉

---

## 🎯 Without OAuth (Even Faster)

Don't want OAuth? **No problem!**

The new advanced UI works immediately without OAuth:

1. Start servers (no .env changes needed)
2. Visit http://localhost:5173/login
3. Enjoy the new design! ✨

OAuth buttons will be visible but won't work until configured.

---

## 📋 Minimum .env for OAuth

```env
# Minimal setup
GOOGLE_CLIENT_ID=your_id_here
GOOGLE_CLIENT_SECRET=your_secret_here
GITHUB_CLIENT_ID=your_id_here
GITHUB_CLIENT_SECRET=your_secret_here
```

---

## 🔍 Quick Troubleshooting

**Issue**: "Redirect URI mismatch"  
**Fix**: Use exact URL: `http://127.0.0.1:8000/accounts/google/login/callback/`

**Issue**: "Site not found"  
**Fix**: Run migrations: `python manage.py migrate`

**Issue**: OAuth buttons don't work  
**Fix**: Check .env credentials are correct, restart server

**Issue**: "Invalid client"  
**Fix**: Verify Client ID and Secret match exactly

---

## 📚 Full Documentation

For detailed instructions: [OAUTH_SETUP_GUIDE.md](./OAUTH_SETUP_GUIDE.md)

---

**Ready in 5 minutes! ⚡**
