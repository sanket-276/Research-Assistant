# OAuth Setup Guide for Research Assistant

## Why OAuth Is Not Working

The Google and GitHub sign-in buttons are not working because:

1. ❌ **Missing OAuth credentials** in `.env` file
2. ❌ **OAuth apps not configured** in the backend
3. ❌ **No redirect URIs registered** with Google/GitHub

## How to Fix It

Follow these steps to enable Google and GitHub OAuth:

---

## Step 1: Get Google OAuth Credentials

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Navigate to **APIs & Services** → **Credentials**
4. Click **+ CREATE CREDENTIALS** → **OAuth 2.0 Client ID**
5. If prompted, configure the OAuth consent screen:
   - User Type: **External**
   - App name: **Research Assistant**
   - User support email: Your email
   - Developer contact: Your email
6. Create OAuth Client ID:
   - Application type: **Web application**
   - Name: **Research Assistant Web Client**
   - Authorized JavaScript origins:
     - `http://localhost:5173`
     - `http://127.0.0.1:5173`
   - Authorized redirect URIs:
     - `http://127.0.0.1:8000/accounts/google/login/callback/`
     - `http://localhost:8000/accounts/google/login/callback/`
7. Click **CREATE**
8. **Copy the Client ID and Client Secret** (you'll need these!)

---

## Step 2: Get GitHub OAuth Credentials

1. Go to [GitHub Settings → Developer Settings](https://github.com/settings/developers)
2. Click **New OAuth App** (or **New GitHub App**)
3. Fill in the application details:
   - **Application name**: Research Assistant
   - **Homepage URL**: `http://localhost:5173`
   - **Authorization callback URL**: `http://127.0.0.1:8000/accounts/github/login/callback/`
4. Click **Register application**
5. **Copy the Client ID**
6. Click **Generate a new client secret**
7. **Copy the Client Secret** (you won't be able to see it again!)

---

## Step 3: Update `.env` File

1. Open the file: `c:\Reasearch_assistant\research_assistant\.env`
2. Replace the placeholder values with your actual credentials:

```env
# OAuth Configuration
GOOGLE_CLIENT_ID=your_actual_google_client_id_here
GOOGLE_CLIENT_SECRET=your_actual_google_client_secret_here
GITHUB_CLIENT_ID=your_actual_github_client_id_here
GITHUB_CLIENT_SECRET=your_actual_github_client_secret_here
```

**Example (with fake credentials):**
```env
GOOGLE_CLIENT_ID=123456789-abcdefghijklmnop.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-1234567890abcdefghijklmnop
GITHUB_CLIENT_ID=Iv1.1234567890abcdef
GITHUB_CLIENT_SECRET=1234567890abcdef1234567890abcdef12345678
```

---

## Step 4: Configure OAuth Apps in Django

Run the setup script:

```bash
cd c:\Reasearch_assistant\research_assistant
python setup_oauth.py
```

This will automatically create the OAuth social apps in Django.

---

## Step 5: Restart the Django Server

1. Stop the current Django server (Ctrl+C)
2. Start it again:

```bash
cd c:\Reasearch_assistant\research_assistant
python manage.py runserver
```

---

## Step 6: Test OAuth Login

1. Go to your frontend: `http://localhost:5173/login`
2. Click **"Continue with Google"** or **"Continue with GitHub"**
3. You should be redirected to the OAuth consent page
4. After authorizing, you'll be redirected back and logged in

---

## Troubleshooting

### "Redirect URI mismatch" error

**Problem:** The redirect URI in Google/GitHub doesn't match.

**Solution:** Make sure you added these exact URIs:
- Google: `http://127.0.0.1:8000/accounts/google/login/callback/`
- GitHub: `http://127.0.0.1:8000/accounts/github/login/callback/`

### "Invalid client" error

**Problem:** Wrong credentials in `.env` file.

**Solution:**
1. Double-check your Client ID and Secret
2. Make sure there are no extra spaces
3. Run `python setup_oauth.py` again

### OAuth redirects but doesn't log in

**Problem:** Session/cookie issues.

**Solution:**
1. Clear browser cookies for localhost
2. Make sure Django server is running on `http://127.0.0.1:8000`
3. Check browser console for errors

### "Application not found" error

**Problem:** OAuth apps not created in Django.

**Solution:**
```bash
python setup_oauth.py
```

---

## Quick Test Without Full Setup

If you want to test OAuth quickly:

1. For now, you can disable the OAuth buttons in the frontend
2. Or use the regular username/password login
3. Set up OAuth later when you have time

To temporarily hide OAuth buttons, you can comment them out in:
- `LoginPage.jsx`
- `SignupPage.jsx`

---

## Current Status

✅ Django site domain configured: `127.0.0.1:8000`
✅ OAuth URLs registered: `/accounts/google/login/` and `/accounts/github/login/`
✅ Setup script created: `setup_oauth.py`
⚠️ **Waiting for OAuth credentials** in `.env` file

Once you add the credentials and run `setup_oauth.py`, OAuth will work!
