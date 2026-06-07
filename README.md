# 🎓 Research Assistant RAG Application

A full-stack AI-powered Research Assistant application with Retrieval-Augmented Generation (RAG) capabilities, built with Django and React.

## ✨ Features

### 🔐 Authentication System
- ✅ **User Registration & Login** - Secure signup and login with token-based authentication
- ✅ **Django Admin Integration** - All user data stored in Django's admin database
- ✅ **Protected Routes** - Frontend routes protected with authentication checks
- ✅ **Session Management** - Automatic token handling and refresh

### 📚 Document Management
- ✅ **PDF Upload** - Upload and process PDF documents
- ✅ **Document Search** - Semantic search across uploaded documents
- ✅ **Auto Summarization** - AI-generated document summaries
- ✅ **Multi-document Support** - Query multiple documents simultaneously

### 💬 Chat Interface
- ✅ **RAG-powered Chat** - Ask questions about your documents
- ✅ **Chat History** - Save and retrieve previous conversations
- ✅ **Streaming Responses** - Real-time streaming of AI responses
- ✅ **Context-aware** - Maintains conversation context

### 🎨 Modern UI/UX
- ✅ **Responsive Design** - Works on desktop, tablet, and mobile
- ✅ **Dark Theme** - Easy on the eyes
- ✅ **Glassmorphism Effects** - Modern, polished interface
- ✅ **Smooth Animations** - Enhanced user experience

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- Node.js 16+
- MySQL Server

### Installation

1. **Clone the repository**
```bash
git clone <repository-url>
cd Reasearch_assistant
```

2. **Backend Setup**
```bash
cd research_assistant
python -m venv venv

# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

pip install -r requirements.txt
```

3. **Configure Database**

Create `.env` file in `research_assistant/`:
```env
DB_NAME=research_assistant_db
DB_USER=root
DB_PASSWORD=your_password
DB_HOST=localhost
DB_PORT=3306
```

Create MySQL database:
```sql
CREATE DATABASE research_assistant_db;
```

4. **Run Migrations**
```bash
python manage.py migrate
python manage.py createsuperuser
```

5. **Frontend Setup**
```bash
cd ../research-assistant-frontend
npm install
```

### Running the Application

**Terminal 1 - Backend:**
```bash
cd research_assistant
venv\Scripts\activate  # or source venv/bin/activate
python manage.py runserver
```

**Terminal 2 - Frontend:**
```bash
cd research-assistant-frontend
npm run dev
```

**Access the application:**
- Frontend: http://localhost:5173
- Backend API: http://127.0.0.1:8000/api
- Admin Panel: http://127.0.0.1:8000/admin

## 📖 Documentation

- [Complete Setup Guide](./SETUP_GUIDE.md) - Detailed installation and configuration
- [Quick Start Guide](./QUICK_START.md) - Quick reference for common tasks

## 🏗️ Architecture

### Backend (Django REST Framework)
```
research_assistant/
├── api/
│   ├── models.py          # User, Document, ChatHistory models
│   ├── views.py           # API endpoints (auth, documents, chat)
│   ├── serializers.py     # Data serializers
│   ├── services.py        # RAG logic, embeddings, LLM integration
│   └── urls.py            # API routes
└── research_assistant/
    └── settings.py        # Django configuration
```

### Frontend (React + Vite)
```
research-assistant-frontend/
├── src/
│   ├── components/        # Reusable UI components
│   ├── context/          
│   │   └── AuthContext.jsx    # Authentication state management
│   ├── pages/            
│   │   ├── LoginPage.jsx      # Login UI
│   │   ├── SignupPage.jsx     # Registration UI
│   │   ├── DashboardPage.jsx  # Main dashboard
│   │   └── ChatPage.jsx       # Chat interface
│   ├── services/
│   │   └── api.js             # API service layer
│   └── App.jsx                # Main app with routing
```

## 🔑 API Endpoints

### Authentication
| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/api/signup/` | POST | No | Register new user |
| `/api/login/` | POST | No | Login user |
| `/api/logout/` | POST | Yes | Logout user |
| `/api/profile/` | GET | Yes | Get user profile |

### Documents
| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/api/documents/` | GET | Yes | List all documents |
| `/api/documents/` | POST | Yes | Upload document |
| `/api/documents/{id}/` | DELETE | Yes | Delete document |
| `/api/documents/{id}/generate_summary/` | POST | Yes | Generate summary |

### Chat
| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/api/chat/start/` | POST | Yes | Start new chat |
| `/api/chat/{id}/message/` | POST | Yes | Send message |
| `/api/chathistories/` | GET | Yes | List chat history |
| `/api/chathistories/{id}/messages/` | GET | Yes | Get chat messages |

### Search
| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/api/search/` | POST | Yes | Search documents |

## 🧪 Testing

### Test Authentication System
```bash
cd research_assistant
python test_auth.py
```

This will test:
- ✅ User signup
- ✅ User login
- ✅ Profile retrieval
- ✅ User logout
- ✅ Invalid credentials handling
- ✅ Unauthorized access protection

### Manual Testing
1. Open http://localhost:5173
2. Create an account via signup page
3. Login with your credentials
4. Upload a PDF document
5. Start a chat and ask questions

## 🗄️ Database Schema

### auth_user (Django User Model)
```sql
- id: Integer (Primary Key)
- username: VARCHAR(150) UNIQUE
- email: VARCHAR(254)
- password: VARCHAR(128) (hashed)
- date_joined: DateTime
- is_active: Boolean
```

### authtoken_token
```sql
- key: VARCHAR(40) (Primary Key)
- user_id: Integer (Foreign Key)
- created: DateTime
```

### api_document
```sql
- id: Integer (Primary Key)
- user_id: Integer (Foreign Key)
- filename: VARCHAR(255)
- file: FileField
- uploaded_at: DateTime
- summary: TextField
```

### api_chathistory
```sql
- id: Integer (Primary Key)
- user_id: Integer (Foreign Key)
- title: VARCHAR(255)
- created_at: DateTime
```

### api_chatmessage
```sql
- id: Integer (Primary Key)
- history_id: Integer (Foreign Key)
- sender: VARCHAR(10) ['user', 'bot']
- message: TextField
- created_at: DateTime
```

## 🔒 Security Features

- ✅ Token-based authentication (Django REST Framework)
- ✅ Password hashing with PBKDF2
- ✅ CORS protection
- ✅ CSRF protection
- ✅ Protected routes
- ✅ Input validation and sanitization
- ✅ SQL injection prevention (Django ORM)

## 🛠️ Technology Stack

### Backend
- Django 5.0
- Django REST Framework
- MySQL
- ChromaDB (vector database)
- Sentence Transformers (embeddings)
- PyMuPDF (PDF processing)
- Torch (ML models)

### Frontend
- React 18
- Vite
- React Router
- Axios
- TailwindCSS
- Context API

## 📊 Features in Detail

### RAG (Retrieval-Augmented Generation)
The application uses a sophisticated RAG pipeline:
1. **Document Processing**: PDFs are chunked and embedded
2. **Vector Storage**: Embeddings stored in ChromaDB
3. **Semantic Search**: Query matching using cosine similarity
4. **Context Retrieval**: Relevant chunks retrieved for each query
5. **Response Generation**: LLM generates contextual answers

### Authentication Flow
1. User signs up → Django creates User in database
2. User logs in → Django generates auth token
3. Token stored in localStorage
4. All API requests include token in headers
5. Backend validates token for each request

## 🐛 Troubleshooting

### Backend Issues

**Database connection error:**
```bash
# Check MySQL is running
mysql -u root -p

# Verify database exists
SHOW DATABASES;
```

**Migration errors:**
```bash
python manage.py migrate --run-syncdb
```

### Frontend Issues

**CORS errors:**
Verify `settings.py` has correct CORS configuration:
```python
CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",
    "http://127.0.0.1:5173",
]
```

**Module not found:**
```bash
npm install
```

## 📝 Admin Panel Usage

### Access Admin Panel
1. Navigate to http://127.0.0.1:8000/admin
2. Login with superuser credentials
3. Manage users, documents, and chat history

### View Registered Users
1. Click "Users" in admin panel
2. See all users registered via signup page
3. Edit user details, permissions, or delete accounts

### Check User's Documents
1. Click "Documents" in admin panel
2. Filter by user to see their uploads
3. View document details and summaries

## 🤝 Contributing

Contributions are welcome! Please follow these steps:
1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## 📄 License

This project is licensed under the MIT License.

## 🙏 Acknowledgments

- Django REST Framework for excellent API tools
- ChromaDB for vector database
- Hugging Face for transformer models
- React team for the amazing frontend framework

## 📧 Support

For issues and questions:
- Check [SETUP_GUIDE.md](./SETUP_GUIDE.md) for detailed instructions
- Review [QUICK_START.md](./QUICK_START.md) for common tasks
- Run `python test_auth.py` to verify setup

---

**Built with ❤️ using Django and React**
