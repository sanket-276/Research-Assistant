# ✅ OAuth MultipleObjectsReturned Error - FIXED!

## Problem Identified

The error `django.core.exceptions.MultipleObjectsReturned` was caused by:

**Duplicate OAuth app configurations** - OAuth credentials were defined in TWO places:
1. ❌ In `settings.py` via `SOCIALACCOUNT_PROVIDERS['APP']` 
2. ✅ In database via `SocialApp` model (Django Admin)

Django-allauth found both configurations and threw a `MultipleObjectsReturned` error.

---

## Solution Applied

### ✅ Fixed `settings.py`

**Removed** the `'APP'` dictionary from `SOCIALACCOUNT_PROVIDERS` in [`settings.py`](c:\Reasearch_assistant\research_assistant\research_assistant\settings.py):

**BEFORE (causing error):**
```python
SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'SCOPE': ['profile', 'email'],
        'APP': {  # ❌ This caused duplicate configuration
            'client_id': env('GOOGLE_CLIENT_ID'),
            'secret': env('GOOGLE_CLIENT_SECRET'),
        }
    },
}
```

**AFTER (fixed):**
```python
SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'SCOPE': ['profile', 'email'],
        # ✅ No 'APP' key - credentials come from database only
    },
}
```

### ✅ Database Configuration Verified

- Google OAuth app exists in database ✅
- GitHub OAuth app exists in database ✅
- Both linked to correct site (`127.0.0.1:8000`) ✅
- No duplicate apps in database ✅

---

## Next Steps to Complete Setup

### 1. **Restart Django Server**

The settings change requires a server restart:

```bash
# Stop the current server (Ctrl+C in the terminal)
# Then restart:
cd c:\Reasearch_assistant\research_assistant
python manage.py runserver
```

### 2. **Verify Redirect URIs** (CRITICAL!)

Make absolutely sure these EXACT redirect URIs are configured:

#### **Google Cloud Console:**
1. Go to: https://console.cloud.google.com/apis/credentials
2. Click on your OAuth 2.0 Client ID: `17151141288-o44prsvan9ejq695jmhd4es98aj1muo4.apps.googleusercontent.com`
3. Under **"Authorized redirect URIs"**, add:
   ```
   http://127.0.0.1:8000/accounts/google/login/callback/
   http://localhost:8000/accounts/google/login/callback/
   ```
4. Click **SAVE**

#### **GitHub OAuth App:**
1. Go to: https://github.com/settings/developers
2. Click on your OAuth app with Client ID: `Ov23lig83415mf3kv8RS`
3. Set **"Authorization callback URL"** to:
   ```
   http://127.0.0.1:8000/accounts/github/login/callback/
   ```
4. Click **Update application**

### 3. **Test OAuth Login**

After restarting the server and verifying redirect URIs:

1. Go to your frontend: `http://localhost:5173/login`
2. Click **"Continue with Google"**
3. Should redirect to Google's consent page ✅
4. After authorization, should redirect back and log you in ✅

Same for GitHub!

---

## Files Modified

1. ✅ [`settings.py`](c:\Reasearch_assistant\research_assistant\research_assistant\settings.py) - Removed duplicate `'APP'` configuration
2. ✅ Database - OAuth apps properly configured via `SocialApp` model
3. ✅ [`fix_oauth_duplicates.py`](c:\Reasearch_assistant\research_assistant\fix_oauth_duplicates.py) - Script to clean up duplicates

---

## Configuration Summary

### Backend Status: ✅ READY

- Django Site: `127.0.0.1:8000` ✅
- Google OAuth App in database ✅
- GitHub OAuth App in database ✅
- Settings.py fixed (no duplicate config) ✅
- OAuth endpoints active:
  - `/accounts/google/login/` ✅
  - `/accounts/github/login/` ✅

### Required Actions: ⚠️ VERIFY

- [ ] Restart Django server
- [ ] Add redirect URIs in Google Cloud Console
- [ ] Add callback URL in GitHub OAuth App
- [ ] Test Google login
- [ ] Test GitHub login

---

## Testing Commands

```bash
# Check OAuth configuration
cd c:\Reasearch_assistant\research_assistant
python check_oauth.py

# Test Google OAuth redirect
curl http://127.0.0.1:8000/api/auth/google/

# Test GitHub OAuth redirect  
curl http://127.0.0.1:8000/api/auth/github/
```

---

## Error Resolution

**Original Error:**
```
django.core.exceptions.MultipleObjectsReturned
```

**Root Cause:**
OAuth credentials defined in both `settings.py` and database.

**Solution:**
Removed `'APP'` dictionary from `SOCIALACCOUNT_PROVIDERS` in `settings.py`. Now using database-only configuration.

**Status:** ✅ **FIXED** - Just restart server and verify redirect URIs!

---

## Quick Reference

**Google Redirect URIs:**
```
http://127.0.0.1:8000/accounts/google/login/callback/
http://localhost:8000/accounts/google/login/callback/
```

**GitHub Callback URL:**
```
http://127.0.0.1:8000/accounts/github/login/callback/
```

**Django Admin** (to manage OAuth apps):
```
http://127.0.0.1:8000/admin/socialaccount/socialapp/
```

---

🎉 **OAuth is now properly configured! Just restart the server and verify redirect URIs.** 🎉
