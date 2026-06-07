# Research Assistant RAG Application - Complete Setup Guide

This guide will walk you through setting up the Research Assistant application with full authentication connected to the Django admin database.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Backend Setup (Django)](#backend-setup-django)
3. [Frontend Setup (React + Vite)](#frontend-setup-react--vite)
4. [Database Configuration](#database-configuration)
5. [Running the Application](#running-the-application)
6. [User Authentication Flow](#user-authentication-flow)
7. [Admin Panel Access](#admin-panel-access)
8. [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before you begin, ensure you have the following installed:

- **Python 3.8+** (Python 3.11 recommended)
- **Node.js 16+** and npm/yarn
- **MySQL Server** (or your preferred database)
- **Git** (for version control)

---

## Backend Setup (Django)

### 1. Navigate to Backend Directory
```bash
cd research_assistant
```

### 2. Create Virtual Environment
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

If `requirements.txt` doesn't exist, install these packages:
```bash
pip install django djangorestframework django-cors-headers mysqlclient python-decouple chromadb sentence-transformers PyMuPDF torch
```

### 4. Create Environment File
Create a `.env` file in the `research_assistant` directory:

```env
# Database Configuration
DB_NAME=research_assistant_db
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_HOST=localhost
DB_PORT=3306

# Optional: OpenAI API Key (if using GPT models)
OPENAI_API_KEY=your_openai_api_key_here
```

### 5. Database Configuration

#### Create MySQL Database
```sql
CREATE DATABASE research_assistant_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 6. Run Database Migrations
```bash
python manage.py makemigrations
python manage.py migrate
```

This will create all necessary tables including:
- `auth_user` (Django's built-in User model)
- `authtoken_token` (for token authentication)
- `api_document` (for uploaded documents)
- `api_chathistory` (for chat sessions)
- `api_chatmessage` (for chat messages)

### 7. Create Admin Superuser
```bash
python manage.py createsuperuser
```

Follow the prompts to create an admin account:
- Username: `admin` (or your choice)
- Email: `admin@example.com`
- Password: (enter a secure password)

### 8. Collect Static Files (Optional for production)
```bash
python manage.py collectstatic --noinput
```

---

## Frontend Setup (React + Vite)

### 1. Navigate to Frontend Directory
```bash
cd research-assistant-frontend
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Verify Configuration
Check that the API base URL in `src/services/api.js` is correct:
```javascript
const API_BASE_URL = 'http://127.0.0.1:8000/api';
```

---

## Running the Application

### Start Backend Server

1. Activate virtual environment (if not already activated):
```bash
# Windows
cd research_assistant
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate
```

2. Run Django development server:
```bash
python manage.py runserver
```

The backend will be available at: **http://127.0.0.1:8000**

### Start Frontend Server

1. Open a new terminal
2. Navigate to frontend directory:
```bash
cd research-assistant-frontend
```

3. Start development server:
```bash
npm run dev
```

The frontend will be available at: **http://localhost:5173**

---

## User Authentication Flow

### How It Works

1. **User Registration (Signup)**
   - User fills out signup form with username, email, and password
   - Frontend sends POST request to `/api/signup/`
   - Django creates a new User in the database
   - User is redirected to login page

2. **User Login**
   - User enters username and password
   - Frontend sends POST request to `/api/login/`
   - Django validates credentials and returns an authentication token
   - Token is stored in localStorage
   - User is redirected to dashboard

3. **Authenticated Requests**
   - All subsequent API requests include the token in headers
   - Django validates the token for each request
   - User data is accessible via `request.user`

4. **User Logout**
   - Frontend calls `/api/logout/`
   - Django deletes the user's token
   - Frontend clears localStorage and redirects to login

### API Endpoints

| Endpoint | Method | Authentication | Description |
|----------|--------|----------------|-------------|
| `/api/signup/` | POST | No | Create new user account |
| `/api/login/` | POST | No | Login and get auth token |
| `/api/logout/` | POST | Yes | Logout and invalidate token |
| `/api/profile/` | GET | Yes | Get user profile data |
| `/api/documents/` | GET/POST | Yes | List/upload documents |
| `/api/chat/start/` | POST | Yes | Start new chat session |
| `/api/search/` | POST | Yes | Search documents |

### Database Schema

#### User Table (auth_user)
```sql
- id: Integer (Primary Key)
- username: VARCHAR(150) UNIQUE
- email: VARCHAR(254)
- password: VARCHAR(128) (hashed)
- first_name: VARCHAR(150)
- last_name: VARCHAR(150)
- is_staff: Boolean
- is_active: Boolean
- date_joined: DateTime
```

#### Token Table (authtoken_token)
```sql
- key: VARCHAR(40) (Primary Key)
- user_id: Integer (Foreign Key to auth_user)
- created: DateTime
```

---

## Admin Panel Access

### Accessing Django Admin

1. Ensure the backend server is running
2. Navigate to: **http://127.0.0.1:8000/admin**
3. Login with your superuser credentials

### Managing Users in Admin

In the admin panel, you can:
- ✅ View all registered users
- ✅ Edit user details (username, email, password)
- ✅ Activate/deactivate user accounts
- ✅ Grant staff or superuser permissions
- ✅ View user's documents and chat history
- ✅ Delete user accounts

### Viewing User Data

To see all users registered through the signup page:
1. Go to Django Admin → Users
2. All users created via signup will be listed here
3. Click on a user to view/edit their details

---

## Testing the Authentication

### Test Signup Flow

1. Open browser to `http://localhost:5173`
2. Click "Create one" to go to signup page
3. Enter:
   - Username: `testuser`
   - Email: `test@example.com`
   - Password: `SecurePass123`
4. Click "Sign Up"
5. Verify success message and redirect to login

### Test Login Flow

1. On login page, enter:
   - Username: `testuser`
   - Password: `SecurePass123`
2. Click "Sign In"
3. Verify redirect to dashboard

### Verify in Database

#### Using Django Shell
```bash
python manage.py shell
```

```python
from django.contrib.auth.models import User

# List all users
users = User.objects.all()
for user in users:
    print(f"Username: {user.username}, Email: {user.email}")

# Get specific user
user = User.objects.get(username='testuser')
print(f"User ID: {user.id}")
print(f"Email: {user.email}")
print(f"Date Joined: {user.date_joined}")

# Check if user has token
from rest_framework.authtoken.models import Token
token = Token.objects.get(user=user)
print(f"Token: {token.key}")
```

#### Using MySQL Command Line
```sql
USE research_assistant_db;

-- View all users
SELECT id, username, email, is_active, date_joined FROM auth_user;

-- View tokens
SELECT t.key, u.username, t.created 
FROM authtoken_token t 
JOIN auth_user u ON t.user_id = u.id;

-- View user's documents
SELECT d.id, d.filename, d.uploaded_at, u.username 
FROM api_document d 
JOIN auth_user u ON d.user_id = u.id;
```

---

## Project Structure

```
Reasearch_assistant/
├── research_assistant/                 # Backend (Django)
│   ├── api/
│   │   ├── models.py                  # Database models (User, Document, Chat)
│   │   ├── views.py                   # API endpoints (signup, login, etc.)
│   │   ├── serializers.py             # Data serializers
│   │   ├── urls.py                    # API routes
│   │   └── services.py                # Business logic (RAG, embeddings)
│   ├── research_assistant/
│   │   ├── settings.py                # Django settings (DB, CORS, etc.)
│   │   └── urls.py                    # Main URL configuration
│   ├── manage.py
│   └── .env                           # Environment variables
│
└── research-assistant-frontend/        # Frontend (React)
    ├── src/
    │   ├── components/                # Reusable components
    │   ├── context/
    │   │   └── AuthContext.jsx        # Authentication context
    │   ├── pages/
    │   │   ├── LoginPage.jsx          # Login page
    │   │   ├── SignupPage.jsx         # Signup page
    │   │   ├── DashboardPage.jsx      # Main dashboard
    │   │   ├── ChatPage.jsx           # Chat interface
    │   │   └── HistoryPage.jsx        # Chat history
    │   ├── services/
    │   │   └── api.js                 # API service layer
    │   ├── App.jsx                    # Main app with routing
    │   └── main.jsx                   # Entry point
    └── package.json
```

---

## Troubleshooting

### Common Issues

#### 1. CORS Errors
**Problem:** Frontend cannot connect to backend

**Solution:** Verify CORS settings in `settings.py`:
```python
CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",
    "http://127.0.0.1:5173",
]
CORS_ALLOW_CREDENTIALS = True
```

#### 2. Database Connection Error
**Problem:** Django cannot connect to MySQL

**Solution:**
- Verify MySQL is running: `mysql -u root -p`
- Check `.env` file credentials
- Ensure database exists: `CREATE DATABASE research_assistant_db;`

#### 3. Token Authentication Not Working
**Problem:** "Authentication credentials were not provided"

**Solution:**
- Check if token is stored in localStorage
- Verify token is sent in request headers
- Check API interceptor in `api.js`

#### 4. User Already Exists Error
**Problem:** Cannot create user with existing username

**Solution:** Use a different username or delete the existing user from admin panel

#### 5. Migration Errors
**Problem:** Migrations fail to apply

**Solution:**
```bash
python manage.py migrate --run-syncdb
```

---

## Security Best Practices

### For Development
- ✅ Use strong passwords for database and admin account
- ✅ Keep `.env` file private (add to `.gitignore`)
- ✅ Use HTTPS in production
- ✅ Enable Django's CSRF protection
- ✅ Validate and sanitize user inputs

### For Production
- 🔒 Set `DEBUG = False` in `settings.py`
- 🔒 Use environment variables for secrets
- 🔒 Configure proper ALLOWED_HOSTS
- 🔒 Use secure database passwords
- 🔒 Enable HTTPS with SSL certificates
- 🔒 Use production-grade database (PostgreSQL recommended)
- 🔒 Implement rate limiting
- 🔒 Regular security updates

---

## Additional Resources

- [Django Documentation](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [React Documentation](https://react.dev/)
- [Vite Documentation](https://vitejs.dev/)

---

## Support

If you encounter any issues:
1. Check the troubleshooting section above
2. Review Django and React console logs
3. Verify database connections and migrations
4. Check network requests in browser DevTools

---

**Happy Coding! 🚀**
