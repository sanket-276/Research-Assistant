# 🔐 OAuth Setup Guide - Google & GitHub Authentication

This guide will help you set up **Google Sign-In** and **GitHub Sign-In** for your Research Assistant application.

---

## 📋 Prerequisites

- Google Account (for Google OAuth)
- GitHub Account (for GitHub OAuth)
- Backend server running
- Admin access to Django admin panel

---

## Part 1: Installing Dependencies

### 1. Install Python Packages

```bash
cd research_assistant
pip install django-allauth==0.57.0 dj-rest-auth==5.0.2
```

### 2. Run Migrations

```bash
python manage.py migrate
```

This creates necessary tables for OAuth (sites, social accounts, etc.)

---

## Part 2: Google OAuth Setup

### Step 1: Create Google OAuth Credentials

1. **Go to Google Cloud Console**
   - Visit: https://console.cloud.google.com/

2. **Create a New Project** (or select existing)
   - Click "Select a Project" → "New Project"
   - Name: "Research Assistant" (or your choice)
   - Click "Create"

3. **Enable Google+ API**
   - Go to "APIs & Services" → "Library"
   - Search for "Google+ API"
   - Click "Enable"

4. **Configure OAuth Consent Screen**
   - Go to "APIs & Services" → "OAuth consent screen"
   - Choose "External" (for testing) or "Internal" (for organization)
   - Fill in required fields:
     - **App name**: Research Assistant
     - **User support email**: Your email
     - **Developer contact**: Your email
   - Click "Save and Continue"
   - **Scopes**: Add `./auth/userinfo.email` and `./auth/userinfo.profile`
   - Click "Save and Continue"
   - **Test users**: Add your email for testing
   - Click "Save and Continue"

5. **Create OAuth 2.0 Client ID**
   - Go to "APIs & Services" → "Credentials"
   - Click "+ CREATE CREDENTIALS" → "OAuth client ID"
   - Application type: **Web application**
   - Name: "Research Assistant Web Client"
   - **Authorized JavaScript origins**:
     ```
     http://localhost:5173
     http://127.0.0.1:8000
     ```
   - **Authorized redirect URIs**:
     ```
     http://127.0.0.1:8000/accounts/google/login/callback/
     http://localhost:5173/auth/callback
     ```
   - Click "Create"

6. **Copy Credentials**
   - You'll see a popup with:
     - **Client ID**: `xxxxx.apps.googleusercontent.com`
     - **Client Secret**: `GOCSPX-xxxxx`
   - **Save these!** You'll need them shortly.

### Step 2: Add Credentials to Django

#### Option A: Using .env File (Recommended)

Add to `.env` file in `research_assistant/`:
```env
# Google OAuth
GOOGLE_CLIENT_ID=your_google_client_id_here.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-your_google_client_secret_here
```

#### Option B: Using Django Admin

1. Start your Django server:
   ```bash
   python manage.py runserver
   ```

2. Go to Django Admin: http://127.0.0.1:8000/admin

3. Navigate to: **Social applications** → **Add social application**

4. Fill in:
   - **Provider**: Google
   - **Name**: Google OAuth
   - **Client id**: [Paste your Google Client ID]
   - **Secret key**: [Paste your Google Client Secret]
   - **Sites**: Select "example.com" (or your site)
   - Click "Save"

---

## Part 3: GitHub OAuth Setup

### Step 1: Create GitHub OAuth App

1. **Go to GitHub Settings**
   - Visit: https://github.com/settings/developers
   - Or: GitHub → Settings → Developer settings → OAuth Apps

2. **Create New OAuth App**
   - Click "New OAuth App"
   - Fill in:
     - **Application name**: Research Assistant
     - **Homepage URL**: `http://localhost:5173`
     - **Application description**: AI Research Assistant with RAG
     - **Authorization callback URL**: 
       ```
       http://127.0.0.1:8000/accounts/github/login/callback/
       ```
   - Click "Register application"

3. **Get Client Credentials**
   - You'll see:
     - **Client ID**: `Iv1.xxxxxxxxxxxxxxxx`
     - Click "Generate a new client secret"
     - **Client Secret**: `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
   - **Save these credentials!**

### Step 2: Add Credentials to Django

#### Option A: Using .env File (Recommended)

Add to `.env` file:
```env
# GitHub OAuth
GITHUB_CLIENT_ID=Iv1.your_github_client_id_here
GITHUB_CLIENT_SECRET=your_github_client_secret_here
```

#### Option B: Using Django Admin

1. Go to: http://127.0.0.1:8000/admin

2. Navigate to: **Social applications** → **Add social application**

3. Fill in:
   - **Provider**: GitHub
   - **Name**: GitHub OAuth
   - **Client id**: [Paste your GitHub Client ID]
   - **Secret key**: [Paste your GitHub Client Secret]
   - **Sites**: Select "example.com"
   - Click "Save"

---

## Part 4: Configure Django Site

### Update Site Domain

1. Go to Django Admin: http://127.0.0.1:8000/admin

2. Navigate to: **Sites**

3. Click on "example.com" (ID: 1)

4. Update:
   - **Domain name**: `127.0.0.1:8000`
   - **Display name**: Research Assistant
   - Click "Save"

---

## Part 5: Complete .env Configuration

Your final `.env` file should look like:

```env
# Database Configuration
DB_NAME=research_assistant_db
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_HOST=localhost
DB_PORT=3306

# Google OAuth
GOOGLE_CLIENT_ID=123456789-abcdefg.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-abcdefghijklmnop

# GitHub OAuth
GITHUB_CLIENT_ID=Iv1.abcdefghijklmnop
GITHUB_CLIENT_SECRET=abcdefghijklmnopqrstuvwxyz1234567890

# Optional: OpenAI API Key
OPENAI_API_KEY=sk-your_openai_key_here
```

---

## Part 6: Testing OAuth

### Test Google Sign-In

1. **Start Backend**:
   ```bash
   cd research_assistant
   python manage.py runserver
   ```

2. **Start Frontend**:
   ```bash
   cd research-assistant-frontend
   npm run dev
   ```

3. **Open App**: http://localhost:5173/login

4. **Click "Continue with Google"**
   - You'll be redirected to Google
   - Select your Google account
   - Grant permissions
   - You'll be redirected back to the app
   - Should be logged in automatically

### Test GitHub Sign-In

1. **Click "Continue with GitHub"**
   - You'll be redirected to GitHub
   - Click "Authorize [App Name]"
   - Grant permissions
   - You'll be redirected back to the app
   - Should be logged in automatically

---

## Part 7: Verify OAuth in Database

### Check Users Created via OAuth

```bash
python manage.py shell
```

```python
from django.contrib.auth.models import User
from allauth.socialaccount.models import SocialAccount

# View all users
User.objects.all()

# View OAuth users
social_accounts = SocialAccount.objects.all()
for account in social_accounts:
    print(f"Provider: {account.provider}, User: {account.user.username}, Email: {account.user.email}")
```

### Check in Django Admin

1. Go to: http://127.0.0.1:8000/admin

2. **Social accounts** → View all OAuth-authenticated users

3. **Users** → See regular + OAuth users

---

## 🔧 Troubleshooting

### Issue: "Redirect URI mismatch"

**Solution:**
- Verify the redirect URI in Google/GitHub matches exactly:
  ```
  http://127.0.0.1:8000/accounts/google/login/callback/
  http://127.0.0.1:8000/accounts/github/login/callback/
  ```
- Note: Must be `127.0.0.1`, not `localhost`

### Issue: "Site matching query does not exist"

**Solution:**
- Run migrations: `python manage.py migrate`
- Check Site ID in admin panel (should be 1)
- Ensure SITE_ID = 1 in settings.py

### Issue: "Social app not configured"

**Solution:**
- Add social app in Django Admin
- OR ensure .env has correct credentials
- Restart Django server after .env changes

### Issue: OAuth button redirects but nothing happens

**Solution:**
- Check browser console for errors
- Verify CORS settings allow `http://localhost:5173`
- Check backend logs for errors

### Issue: "Invalid client" error

**Solution:**
- Verify Client ID and Secret are correct
- Check for extra spaces or newlines in credentials
- Regenerate client secret if needed

---

## 🎨 Customization

### Change OAuth Button Styles

Edit `LoginPage.jsx` or `SignupPage.jsx`:

```jsx
// Google button - change colors
className="bg-white hover:bg-gray-50 text-gray-700"

// GitHub button - change colors  
className="bg-slate-900 hover:bg-slate-800 text-white"
```

### Add More OAuth Providers

Django Allauth supports many providers:
- Facebook
- Twitter
- LinkedIn
- Microsoft
- Apple
- And many more...

To add another provider:
1. Install: `pip install django-allauth`
2. Add to `INSTALLED_APPS` in settings.py
3. Get OAuth credentials from provider
4. Add to Django Admin or .env

---

## 🔒 Security Best Practices

### For Development

✅ Use `.env` file for credentials (never commit to Git)  
✅ Add `.env` to `.gitignore`  
✅ Use `127.0.0.1` instead of `localhost` for consistency  
✅ Test with personal accounts first  

### For Production

🔒 Use environment variables (not .env files)  
🔒 Enable HTTPS (required for OAuth in production)  
🔒 Use production URLs in redirect URIs  
🔒 Set `DEBUG = False`  
🔒 Use secure session cookies  
🔒 Enable CSRF protection  
🔒 Regularly rotate client secrets  

---

## 📊 OAuth Flow Diagram

```
┌─────────────┐
│   User      │
└──────┬──────┘
       │ Clicks "Sign in with Google/GitHub"
       ▼
┌─────────────────────────────────────┐
│   Frontend (React)                  │
│   Redirects to:                     │
│   /api/auth/google/ or             │
│   /api/auth/github/                │
└──────┬──────────────────────────────┘
       │
       ▼
┌─────────────────────────────────────┐
│   Django Backend                    │
│   Redirects to OAuth Provider       │
└──────┬──────────────────────────────┘
       │
       ▼
┌─────────────────────────────────────┐
│   Google/GitHub                     │
│   User logs in & grants permissions │
└──────┬──────────────────────────────┘
       │ Callback with authorization code
       ▼
┌─────────────────────────────────────┐
│   Django Backend                    │
│   - Exchanges code for access token │
│   - Gets user info from provider    │
│   - Creates/logs in Django user     │
│   - Generates auth token            │
└──────┬──────────────────────────────┘
       │ Redirects to frontend
       ▼
┌─────────────────────────────────────┐
│   Frontend                          │
│   - Receives auth token             │
│   - Stores in localStorage          │
│   - Redirects to dashboard          │
└─────────────────────────────────────┘
```

---

## ✅ Verification Checklist

Before going live, verify:

- [ ] Google OAuth credentials configured
- [ ] GitHub OAuth credentials configured
- [ ] Redirect URIs match exactly
- [ ] Site domain configured in Django admin
- [ ] .env file has all credentials
- [ ] Migrations have been run
- [ ] Both frontend and backend are running
- [ ] Can sign in with Google successfully
- [ ] Can sign in with GitHub successfully
- [ ] User appears in Django admin after OAuth login
- [ ] Token is generated and stored
- [ ] User is redirected to dashboard
- [ ] Can access protected routes after OAuth login

---

## 📚 Additional Resources

- [Google OAuth Documentation](https://developers.google.com/identity/protocols/oauth2)
- [GitHub OAuth Documentation](https://docs.github.com/en/developers/apps/building-oauth-apps)
- [Django Allauth Documentation](https://django-allauth.readthedocs.io/)
- [dj-rest-auth Documentation](https://dj-rest-auth.readthedocs.io/)

---

## 🆘 Getting Help

If you're still having issues:

1. Check Django logs: Terminal running `python manage.py runserver`
2. Check browser console: F12 → Console tab
3. Verify OAuth credentials are correct
4. Test with incognito/private window
5. Clear browser cache and cookies
6. Try a different browser

---

**Setup Complete! You now have advanced authentication with Google and GitHub OAuth! 🎉**
