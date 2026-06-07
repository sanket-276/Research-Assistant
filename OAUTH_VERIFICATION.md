# OAuth Configuration Verification ✅

## Status Check

### ✅ Backend Configuration (COMPLETED)
- ✅ OAuth credentials added to `.env` file
- ✅ Google OAuth app created in Django
- ✅ GitHub OAuth app created in Django
- ✅ Django site domain set to `127.0.0.1:8000`
- ✅ OAuth URLs properly configured

### ⚠️ Google Cloud Console (PLEASE VERIFY)

You need to verify these settings in [Google Cloud Console](https://console.cloud.google.com/):

1. **OAuth 2.0 Client ID** for your project:
   - Client ID: `17151141288-o44prsvan9ejq695jmhd4es98aj1muo4.apps.googleusercontent.com` ✅

2. **Authorized JavaScript origins** - Add these:
   ```
   http://localhost:5173
   http://127.0.0.1:5173
   http://localhost:8000
   http://127.0.0.1:8000
   ```

3. **Authorized redirect URIs** - Add these EXACT URIs:
   ```
   http://127.0.0.1:8000/accounts/google/login/callback/
   http://localhost:8000/accounts/google/login/callback/
   ```

### ⚠️ GitHub OAuth App (PLEASE VERIFY)

You need to verify these settings in [GitHub Settings](https://github.com/settings/developers):

1. **OAuth App** with Client ID: `Ov23lig83415mf3kv8RS` ✅

2. **Homepage URL**:
   ```
   http://localhost:5173
   ```

3. **Authorization callback URL** - Add this EXACT URL:
   ```
   http://127.0.0.1:8000/accounts/github/login/callback/
   ```
   
   **IMPORTANT:** GitHub only allows ONE callback URL per app. Make sure it's exactly:
   `http://127.0.0.1:8000/accounts/github/login/callback/`

---

## Testing the Setup

### Step 1: Make sure Django server is running
```bash
cd c:\Reasearch_assistant\research_assistant
python manage.py runserver
```

### Step 2: Make sure React frontend is running
```bash
cd c:\Reasearch_assistant\research-assistant-frontend
npm run dev
```

### Step 3: Test Google OAuth
1. Go to `http://localhost:5173/login`
2. Click "Continue with Google"
3. You should be redirected to Google's consent page
4. After authorizing, you should be logged in

### Step 4: Test GitHub OAuth
1. Go to `http://localhost:5173/login`
2. Click "Continue with GitHub"
3. You should be redirected to GitHub's authorization page
4. After authorizing, you should be logged in

---

## Common Issues & Solutions

### Issue 1: "Redirect URI mismatch" (Google)
**Error:** The redirect URI in the request does not match the ones authorized for the OAuth client.

**Solution:**
1. Go to Google Cloud Console → Credentials
2. Click on your OAuth 2.0 Client ID
3. Under "Authorized redirect URIs", add EXACTLY:
   - `http://127.0.0.1:8000/accounts/google/login/callback/`
   - `http://localhost:8000/accounts/google/login/callback/`
4. Click SAVE
5. Wait 5 minutes for changes to propagate
6. Try again

### Issue 2: "Redirect URI mismatch" (GitHub)
**Error:** The redirect_uri MUST match the registered callback URL for this application.

**Solution:**
1. Go to GitHub Settings → Developer Settings → OAuth Apps
2. Click on your app
3. Set "Authorization callback URL" to EXACTLY:
   `http://127.0.0.1:8000/accounts/github/login/callback/`
4. Click "Update application"
5. Try again

### Issue 3: 500 Internal Server Error
**Possible causes:**
- Missing redirect URIs in Google/GitHub
- Django server not running
- CSRF token issues

**Solution:**
1. Check Django server logs for detailed error
2. Verify redirect URIs are set correctly
3. Clear browser cookies and try again
4. Make sure both frontend and backend servers are running

### Issue 4: OAuth works but doesn't log in
**Problem:** Redirects back but no token/session created

**Solution:**
1. Check browser console for errors
2. Verify CORS settings allow credentials
3. Check Django logs for authentication errors

---

## Quick Verification Commands

Run these to verify your setup:

```bash
# Check OAuth apps in Django
cd c:\Reasearch_assistant\research_assistant
python manage.py shell -c "from allauth.socialaccount.models import SocialApp; [print(f'{app.provider}: {app.client_id}') for app in SocialApp.objects.all()]"

# Check site configuration
python manage.py shell -c "from django.contrib.sites.models import Site; print('Site:', Site.objects.get_current().domain)"

# Test OAuth endpoint
curl http://127.0.0.1:8000/api/auth/google/
```

---

## Next Steps

1. ✅ Verify redirect URIs in Google Cloud Console
2. ✅ Verify callback URL in GitHub OAuth App  
3. ✅ Restart Django server: `python manage.py runserver`
4. ✅ Test OAuth login from frontend

**Once you've verified the redirect URIs in Google and GitHub, OAuth should work perfectly!**

---

## Configuration Summary

✅ **Google OAuth App**
- Client ID: `17151141288-o44prsvan9ejq695jmhd4es98aj1muo4.apps.googleusercontent.com`
- Status: Configured in Django ✅
- Redirect URIs: ⚠️ Please verify in Google Cloud Console

✅ **GitHub OAuth App**
- Client ID: `Ov23lig83415mf3kv8RS`
- Status: Configured in Django ✅
- Callback URL: ⚠️ Please verify in GitHub Settings

✅ **Django Configuration**
- Site: `127.0.0.1:8000` ✅
- OAuth endpoints: `/accounts/google/login/` and `/accounts/github/login/` ✅
- Token authentication: Enabled ✅

**Everything is ready on the Django side! Just verify the redirect URIs on Google/GitHub and you're all set! 🚀**
