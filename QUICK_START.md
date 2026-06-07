# Research Assistant - Quick Reference

## Quick Start Commands

### Backend (Django)
```bash
# 1. Navigate to backend
cd research_assistant

# 2. Activate virtual environment
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# 3. Run server
python manage.py runserver
```

### Frontend (React)
```bash
# 1. Navigate to frontend
cd research-assistant-frontend

# 2. Start dev server
npm run dev
```

## Access Points

- **Frontend:** http://localhost:5173
- **Backend API:** http://127.0.0.1:8000/api
- **Admin Panel:** http://127.0.0.1:8000/admin

## Authentication Endpoints

| Endpoint | Method | Body | Response |
|----------|--------|------|----------|
| `/api/signup/` | POST | `{username, email, password}` | `{token, user_id, username}` |
| `/api/login/` | POST | `{username, password}` | `{token, user_id, username}` |
| `/api/logout/` | POST | (authenticated) | `{message}` |
| `/api/profile/` | GET | (authenticated) | `{id, username, email}` |

## Testing Authentication

### 1. Create Test User via Signup Page
- Navigate to http://localhost:5173/signup
- Fill in the form
- Submit

### 2. Verify in Django Admin
- Go to http://127.0.0.1:8000/admin
- Login with superuser credentials
- Click "Users" to see all registered users

### 3. Check Database Directly
```bash
python manage.py shell
```

```python
from django.contrib.auth.models import User
User.objects.all()  # List all users
```

## User Data Storage

All user details are saved in the Django database:

- **Table:** `auth_user` (Django's built-in User model)
- **Fields:** id, username, email, password (hashed), date_joined, etc.
- **Location:** Your MySQL database configured in `.env`

## Common Tasks

### Create Superuser
```bash
python manage.py createsuperuser
```

### View All Users (Django Shell)
```bash
python manage.py shell
```
```python
from django.contrib.auth.models import User
for user in User.objects.all():
    print(f"{user.username} - {user.email}")
```

### Reset User Password (Django Shell)
```python
from django.contrib.auth.models import User
user = User.objects.get(username='testuser')
user.set_password('newpassword')
user.save()
```

### Delete User
```python
from django.contrib.auth.models import User
User.objects.get(username='testuser').delete()
```

## File Structure

```
Backend Authentication Files:
├── api/views.py          # SignUpView, LoginView, LogoutView
├── api/urls.py           # Authentication routes
├── api/serializers.py    # UserSerializer
└── settings.py           # CORS, Auth config

Frontend Authentication Files:
├── src/context/AuthContext.jsx   # Authentication state
├── src/services/api.js           # API service layer
├── src/pages/LoginPage.jsx       # Login UI
├── src/pages/SignupPage.jsx      # Signup UI
└── src/App.jsx                   # Protected routes
```

## Environment Variables (.env)

```env
DB_NAME=research_assistant_db
DB_USER=root
DB_PASSWORD=your_password
DB_HOST=localhost
DB_PORT=3306
```

## Troubleshooting Quick Fixes

### CORS Error
Check `settings.py`:
```python
CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",
    "http://127.0.0.1:5173",
]
```

### Token Not Working
Clear browser localStorage and login again

### Database Connection Failed
1. Check MySQL is running
2. Verify `.env` credentials
3. Run migrations: `python manage.py migrate`

### Frontend Can't Connect
1. Ensure backend is running on port 8000
2. Check API_BASE_URL in `src/services/api.js`

---

**Need detailed instructions? See [SETUP_GUIDE.md](./SETUP_GUIDE.md)**
