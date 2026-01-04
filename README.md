# Telegram Bot

A comprehensive Telegram bot application with advanced features including user management, database integration, and command-based interactions.

## 📋 Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Folder Structure](#folder-structure)
- [Commands](#commands)
- [Database Schema](#database-schema)
- [Configuration](#configuration)
- [Disclaimer](#disclaimer)

## ✨ Features

- **User Management**: Complete user registration and profile management system
- **Command-Based Interaction**: Intuitive command system for bot operations
- **Database Integration**: Persistent data storage with SQLite/PostgreSQL support
- **Error Handling**: Comprehensive error handling and logging
- **Message Parsing**: Advanced message parsing and validation
- **Admin Controls**: Administrative functions for bot management
- **Inline Queries**: Support for inline query responses
- **Callback Handlers**: Interactive callback query handling
- **Rate Limiting**: Built-in rate limiting for API calls
- **Logging System**: Detailed logging for debugging and monitoring

## 🚀 Installation

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)
- Telegram Bot API Token (obtain from [@BotFather](https://t.me/BotFather))
- Database (SQLite or PostgreSQL)

### Setup Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/Ryekagosaki/Telegram-Bot.git
   cd Telegram-Bot
   ```

2. **Create a virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your settings (see Configuration section)
   ```

5. **Initialize the database**
   ```bash
   python init_db.py
   ```

6. **Run the bot**
   ```bash
   python main.py
   ```

## 📁 Folder Structure

```
Telegram-Bot/
├── main.py                 # Entry point of the application
├── requirements.txt        # Python dependencies
├── .env.example           # Example environment variables
├── README.md              # This file
│
├── config/
│   ├── __init__.py
│   ├── settings.py        # Configuration and settings
│   └── constants.py       # Application constants
│
├── handlers/
│   ├── __init__.py
│   ├── command_handlers.py    # Command handler implementations
│   ├── message_handlers.py    # Message handler implementations
│   ├── callback_handlers.py   # Callback query handlers
│   └── error_handlers.py      # Error handling logic
│
├── models/
│   ├── __init__.py
│   ├── user.py            # User data model
│   ├── message.py         # Message data model
│   └── base.py            # Base model class
│
├── database/
│   ├── __init__.py
│   ├── db.py              # Database connection and utilities
│   ├── migrations/        # Database migration files
│   └── queries/           # SQL query definitions
│
├── utils/
│   ├── __init__.py
│   ├── logger.py          # Logging utilities
│   ├── validators.py      # Input validation functions
│   ├── helpers.py         # Helper functions
│   └── decorators.py      # Custom decorators
│
├── keyboards/
│   ├── __init__.py
│   ├── inline.py          # Inline keyboard definitions
│   └── reply.py           # Reply keyboard definitions
│
├── services/
│   ├── __init__.py
│   ├── user_service.py    # User-related business logic
│   ├── message_service.py # Message-related business logic
│   └── api_service.py     # External API integrations
│
└── tests/
    ├── __init__.py
    ├── test_handlers.py    # Handler unit tests
    ├── test_models.py      # Model unit tests
    └── test_utils.py       # Utility unit tests
```

## 🎮 Commands

### User Commands

| Command | Description | Usage |
|---------|-------------|-------|
| `/start` | Initialize the bot and user registration | `/start` |
| `/help` | Display available commands and help information | `/help` |
| `/profile` | View user profile information | `/profile` |
| `/settings` | Manage user settings | `/settings` |
| `/stats` | Display user statistics | `/stats` |
| `/about` | Information about the bot | `/about` |

### Admin Commands

| Command | Description | Usage | Permission |
|---------|-------------|-------|-----------|
| `/admin` | Access admin panel | `/admin` | Admin only |
| `/users` | View all registered users | `/users` | Admin only |
| `/ban` | Ban a user | `/ban <user_id>` | Admin only |
| `/unban` | Unban a user | `/unban <user_id>` | Admin only |
| `/stats_all` | View global statistics | `/stats_all` | Admin only |
| `/broadcast` | Send message to all users | `/broadcast <message>` | Admin only |

### Utility Commands

| Command | Description | Usage |
|---------|-------------|-------|
| `/clear` | Clear user data | `/clear` |
| `/export` | Export user data | `/export` |
| `/feedback` | Send feedback about the bot | `/feedback <message>` |

## 🗄️ Database Schema

### Users Table

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    telegram_id BIGINT UNIQUE NOT NULL,
    username VARCHAR(255),
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    is_active BOOLEAN DEFAULT TRUE,
    is_admin BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_interaction TIMESTAMP
);
```

### Messages Table

```sql
CREATE TABLE messages (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER NOT NULL,
    message_text TEXT,
    message_type VARCHAR(50),
    is_processed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### User Sessions Table

```sql
CREATE TABLE user_sessions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER NOT NULL UNIQUE,
    session_token VARCHAR(255) UNIQUE,
    state VARCHAR(100),
    data JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### Bot Settings Table

```sql
CREATE TABLE bot_settings (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    setting_key VARCHAR(255) UNIQUE NOT NULL,
    setting_value TEXT,
    description TEXT,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Activity Log Table

```sql
CREATE TABLE activity_log (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER,
    action VARCHAR(255) NOT NULL,
    details TEXT,
    ip_address VARCHAR(45),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL
);
```

## ⚙️ Configuration

### Environment Variables

Create a `.env` file in the root directory with the following variables:

```env
# Bot Configuration
TELEGRAM_BOT_TOKEN=your_bot_token_here
BOT_NAME=YourBotName
BOT_DESCRIPTION=Your bot description

# Database Configuration
DATABASE_TYPE=sqlite  # or postgresql
DATABASE_URL=sqlite:///bot.db
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_USER=postgres
DATABASE_PASSWORD=your_password
DATABASE_NAME=telegram_bot_db

# Logging Configuration
LOG_LEVEL=INFO
LOG_FILE=logs/bot.log
LOG_FORMAT=%(asctime)s - %(name)s - %(levelname)s - %(message)s

# Admin Configuration
ADMIN_IDS=123456789,987654321  # Comma-separated admin user IDs
ALLOWED_CHATS=-1001234567890,123456789  # Allowed chat IDs

# API Configuration
API_TIMEOUT=30
MAX_RETRIES=3
RATE_LIMIT_CALLS=30
RATE_LIMIT_PERIOD=60

# Feature Flags
ENABLE_INLINE_QUERIES=true
ENABLE_CALLBACK_QUERIES=true
ENABLE_FILE_PROCESSING=true
ENABLE_LOGGING=true

# Security
JWT_SECRET=your_jwt_secret_key
PASSWORD_HASH_ALGORITHM=bcrypt
SESSION_TIMEOUT=3600

# Development
DEBUG=false
TEST_MODE=false
```

### Loading Configuration

Configuration is automatically loaded from the `.env` file using the `python-dotenv` library:

```python
from config.settings import settings

bot_token = settings.TELEGRAM_BOT_TOKEN
db_url = settings.DATABASE_URL
```

## ⚠️ Disclaimer

### Important Notice

1. **Educational Purpose**: This bot is provided as-is for educational and development purposes. Use it at your own risk.

2. **Telegram API Compliance**: Ensure your bot complies with the [Telegram Bot API Terms of Service](https://core.telegram.org/bots/api). Misuse of the API may result in your bot being blocked.

3. **Data Privacy**: This bot may collect and store user data. You are responsible for:
   - Complying with applicable data protection laws (GDPR, CCPA, etc.)
   - Implementing appropriate security measures
   - Providing clear privacy policies to users
   - Handling user data responsibly and securely

4. **Security**: 
   - Never commit the `.env` file or sensitive configuration to version control
   - Keep your bot token confidential
   - Regularly update dependencies to patch security vulnerabilities
   - Implement rate limiting and input validation

5. **Rate Limiting**: Be mindful of Telegram API rate limits:
   - Limit yourself to no more than 30 messages per second
   - Implement exponential backoff for retries
   - Monitor your API usage

6. **Terms of Service**: The creators and contributors of this project are not responsible for:
   - Misuse of the bot or its functionality
   - Violations of Telegram's Terms of Service
   - Data breaches or security incidents
   - Any legal consequences arising from improper use

7. **Liability**: This project is provided "AS IS" without warranty of any kind. The authors and contributors shall not be liable for any direct, indirect, incidental, special, exemplary, or consequential damages.

8. **No Warranty**: The software is provided without any guarantees of functionality, security, or compatibility.

9. **User Responsibility**: Users of this bot are solely responsible for:
   - Ensuring compliance with local laws and regulations
   - Protecting sensitive user data
   - Maintaining backups of critical data
   - Testing thoroughly before deployment to production

---

## 📞 Support and Contributing

- **Issues**: Report bugs and request features through GitHub Issues
- **Pull Requests**: Contributions are welcome! Please follow the project's coding standards
- **Documentation**: Help improve the documentation by submitting improvements

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

**Last Updated**: 2026-01-04

For questions or support, please open an issue on the [GitHub repository](https://github.com/Ryekagosaki/Telegram-Bot).
