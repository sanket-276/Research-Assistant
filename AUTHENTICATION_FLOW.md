# Authentication Flow Diagram

## System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         USER BROWSER                                 │
│                                                                      │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────┐       │
│  │  LoginPage     │  │  SignupPage    │  │  Dashboard     │       │
│  │                │  │                │  │  (Protected)   │       │
│  └────────┬───────┘  └────────┬───────┘  └────────┬───────┘       │
│           │                   │                    │                │
│           └───────────────────┴────────────────────┘                │
│                               │                                     │
│                    ┌──────────▼──────────┐                          │
│                    │   AuthContext       │                          │
│                    │   - login()         │                          │
│                    │   - signup()        │                          │
│                    │   - logout()        │                          │
│                    │   - token           │                          │
│                    │   - user            │                          │
│                    └──────────┬──────────┘                          │
│                               │                                     │
│                    ┌──────────▼──────────┐                          │
│                    │   API Service       │                          │
│                    │   (api.js)          │                          │
│                    │   - authAPI         │                          │
│                    │   - documentAPI     │                          │
│                    │   - chatAPI         │                          │
│                    └──────────┬──────────┘                          │
│                               │                                     │
└───────────────────────────────┼─────────────────────────────────────┘
                                │
                      HTTP/HTTPS (Token in Headers)
                                │
┌───────────────────────────────▼─────────────────────────────────────┐
│                      DJANGO BACKEND                                  │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────┐       │
│  │                    API Endpoints                         │       │
│  │  /api/signup/     /api/login/     /api/logout/          │       │
│  │  /api/profile/    /api/documents/ /api/chat/            │       │
│  └─────────────────────────┬───────────────────────────────┘       │
│                            │                                        │
│  ┌─────────────────────────▼───────────────────────────────┐       │
│  │              Token Authentication                        │       │
│  │         (rest_framework.authtoken)                       │       │
│  │    - Validate token on each request                     │       │
│  │    - Identify user from token                            │       │
│  └─────────────────────────┬───────────────────────────────┘       │
│                            │                                        │
│  ┌─────────────────────────▼───────────────────────────────┐       │
│  │                  Views & Business Logic                  │       │
│  │  - SignUpView: Create user + token                      │       │
│  │  - LoginView: Validate + return token                   │       │
│  │  - LogoutView: Delete token                             │       │
│  │  - Protected views: Require authentication              │       │
│  └─────────────────────────┬───────────────────────────────┘       │
│                            │                                        │
└────────────────────────────┼────────────────────────────────────────┘
                             │
                             │
┌────────────────────────────▼────────────────────────────────────────┐
│                      MySQL DATABASE                                  │
│                                                                      │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐ │
│  │   auth_user      │  │ authtoken_token  │  │  api_document    │ │
│  │  - id            │  │  - key           │  │  - id            │ │
│  │  - username      │  │  - user_id (FK)  │  │  - user_id (FK)  │ │
│  │  - email         │  │  - created       │  │  - filename      │ │
│  │  - password      │  └──────────────────┘  │  - file          │ │
│  │  - date_joined   │                        └──────────────────┘ │
│  └──────────────────┘                                              │
│                                                                      │
│  ┌──────────────────┐  ┌──────────────────┐                        │
│  │ api_chathistory  │  │ api_chatmessage  │                        │
│  │  - id            │  │  - id            │                        │
│  │  - user_id (FK)  │  │  - history_id    │                        │
│  │  - title         │  │  - sender        │                        │
│  │  - created_at    │  │  - message       │                        │
│  └──────────────────┘  └──────────────────┘                        │
└─────────────────────────────────────────────────────────────────────┘
```

## User Registration Flow

```
User fills signup form
        │
        ▼
Frontend: authAPI.signup(username, email, password)
        │
        ▼
POST /api/signup/ {username, email, password}
        │
        ▼
Backend: SignUpView.post()
        │
        ├─> Validate data (UserSerializer)
        │
        ├─> Create User in database
        │   └─> Password hashed with PBKDF2
        │
        ├─> Create Token for user
        │
        └─> Return {token, user_id, username}
        │
        ▼
Frontend: Show success message
        │
        ▼
Redirect to login page
```

## User Login Flow

```
User enters credentials
        │
        ▼
Frontend: authAPI.login(username, password)
        │
        ▼
POST /api/login/ {username, password}
        │
        ▼
Backend: LoginView.post()
        │
        ├─> authenticate(username, password)
        │   └─> Verify credentials against database
        │
        ├─> Get or create Token for user
        │
        └─> Return {token, user_id, username}
        │
        ▼
Frontend: AuthContext.login()
        │
        ├─> Store token in localStorage
        │
        ├─> Update auth state (user, token)
        │
        └─> Redirect to dashboard
```

## Authenticated Request Flow

```
User makes request (e.g., upload document)
        │
        ▼
Frontend: documentAPI.uploadDocument(file)
        │
        ▼
API Interceptor adds header:
Authorization: Token abc123xyz...
        │
        ▼
POST /api/documents/ (with file)
        │
        ▼
Backend: Token Authentication Middleware
        │
        ├─> Extract token from header
        │
        ├─> Lookup token in database
        │
        ├─> Get associated user
        │
        └─> Set request.user
        │
        ▼
Backend: DocumentViewSet.perform_create()
        │
        ├─> Access request.user
        │
        ├─> Save document with user foreign key
        │
        └─> Return document data
        │
        ▼
Frontend: Update UI with new document
```

## User Logout Flow

```
User clicks logout
        │
        ▼
Frontend: authAPI.logout()
        │
        ▼
POST /api/logout/ (with token in header)
        │
        ▼
Backend: LogoutView.post()
        │
        ├─> Get request.user.auth_token
        │
        ├─> Delete token from database
        │
        └─> Return success message
        │
        ▼
Frontend: AuthContext.logout()
        │
        ├─> Clear localStorage
        │   ├─> Remove authToken
        │   ├─> Remove authUserId
        │   └─> Remove authUsername
        │
        ├─> Clear auth state (user, token)
        │
        └─> Redirect to login page
```

## Protected Route Flow

```
User navigates to /dashboard
        │
        ▼
App.jsx: Check authentication
        │
        ├─> isAuthenticated?
        │   │
        │   ├─ YES ─> Render Dashboard
        │   │
        │   └─ NO ──> Redirect to /login
        │
        ▼
If authenticated, Dashboard loads
        │
        ▼
Dashboard makes API calls
        │
        ├─> Token automatically added by interceptor
        │
        └─> Backend validates token
            │
            ├─ Valid ──> Return data
            │
            └─ Invalid ─> Return 401
                          │
                          ▼
                    Interceptor catches 401
                          │
                          ├─> Clear localStorage
                          │
                          └─> Redirect to /login
```

## Token Storage and Security

### Frontend (Browser)
```
localStorage:
  ├─ authToken: "abc123xyz..."     (40 char token)
  ├─ authUserId: "5"                (user ID)
  └─ authUsername: "john_doe"       (username)

All API requests include:
  Authorization: Token abc123xyz...
```

### Backend (Database)
```
authtoken_token table:
  ├─ key: "abc123xyz..." (Primary Key, indexed)
  ├─ user_id: 5          (Foreign Key to auth_user)
  └─ created: 2025-10-14 10:30:00

One token per user
Token deleted on logout
```

## Data Relationships

```
User (auth_user)
    │
    ├─── Has One ────> Token (authtoken_token)
    │
    ├─── Has Many ──> Documents (api_document)
    │
    └─── Has Many ──> Chat Histories (api_chathistory)
                         │
                         └─── Has Many ──> Chat Messages (api_chatmessage)
```

## Security Layers

```
┌─────────────────────────────────────────────────────────┐
│  Layer 1: Frontend Route Protection                     │
│  - Check isAuthenticated before rendering               │
│  - Redirect unauthenticated users to login              │
└─────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Layer 2: API Request Validation                        │
│  - Token must be present in headers                     │
│  - Interceptor handles 401 responses                    │
└─────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Layer 3: Backend Token Authentication                  │
│  - Validate token exists in database                    │
│  - Verify token belongs to active user                  │
└─────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Layer 4: View-Level Permission Checks                  │
│  - permissions.IsAuthenticated required                 │
│  - User can only access their own data                  │
└─────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Layer 5: Database Constraints                          │
│  - Foreign keys enforce relationships                   │
│  - Unique constraints prevent duplicates                │
└─────────────────────────────────────────────────────────┘
```

## Error Handling

```
┌─────────────────────────────────────────────────────────┐
│  Signup Error: Username already exists                  │
│  - Backend returns 400 with error message               │
│  - Frontend displays: "Username already taken"          │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│  Login Error: Invalid credentials                       │
│  - Backend returns 400 with error message               │
│  - Frontend displays: "Invalid username or password"    │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│  Auth Error: Invalid or expired token                   │
│  - Backend returns 401 Unauthorized                     │
│  - Interceptor clears localStorage                      │
│  - Redirect to login page                               │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│  Network Error: Backend unreachable                     │
│  - Request fails with network error                     │
│  - Frontend displays: "Cannot connect to server"        │
└─────────────────────────────────────────────────────────┘
```

## Complete User Journey

```
1. First Visit
   └─> User not authenticated
       └─> Redirect to /login

2. Click "Create Account"
   └─> Navigate to /signup
       └─> Fill form and submit
           └─> User created in database
               └─> Redirect to /login

3. Login
   └─> Enter credentials
       └─> Token generated and stored
           └─> Auth state updated
               └─> Redirect to /dashboard

4. Use Application
   └─> Upload documents
   └─> Create chats
   └─> Ask questions
   └─> All requests authenticated with token

5. Close Browser
   └─> Token persists in localStorage
       └─> User still authenticated on return

6. Logout
   └─> Token deleted from database
       └─> localStorage cleared
           └─> Redirect to /login
```

---

**This authentication system provides secure, token-based authentication with full database integration!**
