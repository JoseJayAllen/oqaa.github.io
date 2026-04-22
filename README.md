# DocuManage Backend

A production-ready Flask backend for the DocuManage Document Management System with MySQL database support.

## Features

- **Authentication & Authorization**: JWT-based authentication with role-based access control (Admin, Editor, Viewer)
- **File Management**: Upload, download, share, and manage documents with metadata
- **Audit Trail**: Complete activity logging for security and compliance
- **Notifications**: Real-time notifications and messaging system
- **User Management**: Full CRUD operations for user accounts (Admin only)
- **Dashboard**: Aggregated statistics and recent activity
- **Security**: Password hashing, rate limiting, CORS protection
- **API Documentation**: RESTful API with consistent response format

## Tech Stack

- **Framework**: Flask 3.0
- **Database**: MySQL (with PyMySQL driver)
- **ORM**: SQLAlchemy
- **Authentication**: Flask-JWT-Extended
- **Migration**: Flask-Migrate (Alembic)
- **Validation**: Marshmallow
- **Security**: Werkzeug, Flask-Limiter

## Project Structure

```
docmgmt-backend/
├── app/
│   ├── __init__.py          # Application factory
│   ├── models/              # Database models
│   │   ├── __init__.py
│   │   ├── user.py          # User model
│   │   ├── file.py          # File model
│   │   ├── audit_log.py     # Audit log model
│   │   ├── notification.py  # Notification model
│   │   └── message.py       # Message model
│   ├── routes/              # API routes
│   │   ├── __init__.py
│   │   ├── auth.py          # Authentication routes
│   │   ├── users.py         # User management routes
│   │   ├── files.py         # File management routes
│   │   ├── audit.py         # Audit log routes
│   │   ├── notifications.py # Notification routes
│   │   └── dashboard.py     # Dashboard routes
│   └── utils/               # Utility functions
│       ├── error_handlers.py
│       └── init_db.py
├── config/                  # Configuration files
│   └── config.py
├── uploads/                 # File upload directory
├── migrations/              # Database migrations
├── tests/                   # Test files
├── run.py                   # Application entry point
├── setup_database.py        # Database setup script
├── requirements.txt         # Python dependencies
├── .env.example             # Environment variables template
└── README.md                # This file
```

## Installation

### Prerequisites

- Python 3.8+
- MySQL 5.7+ or MySQL Workbench
- PyCharm (recommended IDE)

### Step 1: Clone and Setup

```bash
# Extract the backend folder to your PyCharm projects directory
cd docmgmt-backend

# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

### Step 2: Database Setup

#### Option A: Using Setup Script (Recommended)

```bash
python setup_database.py
```

This interactive script will:
- Create the database
- Create a database user
- Grant necessary privileges
- Create the `.env` file

#### Option B: Manual Setup (MySQL Workbench)

1. Open MySQL Workbench
2. Connect to your MySQL server
3. Execute the following SQL:

```sql
-- Create database
CREATE DATABASE IF NOT EXISTS documanage_db 
CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;

-- Create user
CREATE USER IF NOT EXISTS 'documanage_user'@'%' 
IDENTIFIED BY 'documanage_pass';

-- Grant privileges
GRANT ALL PRIVILEGES ON documanage_db.* 
TO 'documanage_user'@'%';

FLUSH PRIVILEGES;
```

4. Create `.env` file from template:

```bash
cp .env.example .env
# Edit .env with your database credentials
```

### Step 3: Initialize Database

```bash
# Create tables
flask db init      # Only first time
flask db migrate   # Create migration
flask db upgrade   # Apply migration

# Or use the custom command
flask init-db      # Creates tables and demo data
```

### Step 4: Run the Application

```bash
# Development mode
python run.py

# Or using Flask CLI
flask run

# Production mode (with Gunicorn)
gunicorn -w 4 -b 0.0.0.0:5000 "app:create_app('production')"
```

The API will be available at `http://localhost:5000`

## API Endpoints

### Authentication

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Register new user |
| POST | `/api/auth/login` | User login |
| POST | `/api/auth/refresh` | Refresh access token |
| POST | `/api/auth/logout` | User logout |
| GET | `/api/auth/me` | Get current user |
| POST | `/api/auth/change-password` | Change password (Admin only) |

### Users (Admin only)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/users/` | List all users |
| GET | `/api/users/<id>` | Get user details |
| PUT | `/api/users/<id>` | Update user |
| DELETE | `/api/users/<id>` | Delete user |
| GET | `/api/users/stats` | User statistics |

### Files

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/files/` | List files |
| GET | `/api/files/<id>` | Get file details |
| POST | `/api/files/upload` | Upload file |
| GET | `/api/files/<id>/download` | Download file |
| DELETE | `/api/files/<id>` | Delete file |
| POST | `/api/files/<id>/share` | Share file |
| POST | `/api/files/<id>/unshare` | Unshare file |
| GET | `/api/files/stats` | File statistics |

### Audit Log

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/audit/` | Get audit logs |
| GET | `/api/audit/stats` | Activity statistics |
| GET | `/api/audit/my-activity` | Current user activity |

### Notifications & Messages

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/notifications/notifications` | Get notifications |
| POST | `/api/notifications/notifications/<id>/read` | Mark notification read |
| POST | `/api/notifications/notifications/read-all` | Mark all read |
| GET | `/api/notifications/messages` | Get messages |
| POST | `/api/notifications/messages` | Send message |
| POST | `/api/notifications/messages/<id>/read` | Mark message read |

### Dashboard

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/dashboard/` | Get dashboard data |
| GET | `/api/dashboard/stats` | Get statistics |

## User Roles & Permissions

| Role | Upload | Delete | Manage Accounts | Change Password | Edit Profile |
|------|--------|--------|-----------------|-----------------|--------------|
| Admin | ✓ | ✓ | ✓ | ✓ | ✓ |
| Editor | ✓ | ✓ | ✗ | ✗ | ✗ |
| Viewer | ✗ | ✗ | ✗ | ✗ | ✗ |

## Default Users

After running `flask init-db`, the following demo users are created:

| Email | Password | Role |
|-------|----------|------|
| admin@documanage.com | admin123 | Admin |
| editor@documanage.com | editor123 | Editor |
| viewer@documanage.com | viewer123 | Viewer |

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `FLASK_ENV` | Environment mode | `development` |
| `SECRET_KEY` | Flask secret key | Required |
| `JWT_SECRET_KEY` | JWT signing key | Required |
| `DATABASE_URL` | MySQL connection string | Required |
| `MAX_CONTENT_LENGTH` | Max upload size | `104857600` (100MB) |
| `UPLOAD_FOLDER` | Upload directory | `uploads` |
| `MAIL_SERVER` | SMTP server | Optional |
| `MAIL_PORT` | SMTP port | `587` |
| `CORS_ORIGINS` | Allowed CORS origins | `*` |

## Database Schema

### Users Table
- `id` (PK)
- `first_name`, `last_name`, `email`
- `password_hash`
- `role` (admin/editor/viewer)
- `is_active`, `is_verified`
- `department`, `employee_id`
- `created_at`, `updated_at`, `last_login`

### Files Table
- `id` (PK)
- `original_filename`, `stored_filename`
- `file_path`, `file_size`, `file_type`
- `user_id` (FK)
- `is_shared`, `shared_with`
- `is_deleted`, `deleted_at`
- `created_at`, `updated_at`

### Audit Logs Table
- `id` (PK)
- `action`, `description`
- `user_id` (FK)
- `target_type`, `target_id`
- `ip_address`, `user_agent`
- `status`, `created_at`

## Testing

```bash
# Run tests
pytest

# Run with coverage
pytest --cov=app tests/

# Run specific test file
pytest tests/test_auth.py
```

## Deployment

### Using Docker

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:5000", "run:app"]
```

### Using Gunicorn

```bash
gunicorn -w 4 -b 0.0.0.0:5000 "app:create_app('production')"
```

## Troubleshooting

### MySQL Connection Issues

```bash
# Test MySQL connection
mysql -u documanage_user -p -h localhost documanage_db

# Check if PyMySQL is installed
pip show PyMySQL

# Install cryptography for MySQL 8.0+
pip install cryptography
```

### Database Migration Issues

```bash
# Reset migrations
rm -rf migrations/
flask db init
flask db migrate -m "Initial migration"
flask db upgrade
```

## License

MIT License

## Support

For issues and questions, please refer to the project documentation or contact the development team.
