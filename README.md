# Kuesioner - Customer Satisfaction Survey System

A web-based customer satisfaction survey application where customers can fill out questionnaires and admins can manage questions and view satisfaction analytics.

## Features

- Customer-facing survey forms with star ratings
- Admin panel for questionnaire and question management
- Real-time satisfaction index calculation
- JWT-based admin authentication
- Mobile-responsive design
- Rate limiting for form submissions

## Tech Stack

- **Backend**: FastAPI (Python 3.10+)
- **Database**: MySQL 8.0+
- **ORM**: SQLAlchemy
- **Frontend**: Jinja2 templates + HTML/CSS/Vanilla JS
- **Authentication**: JWT tokens
- **Password Hashing**: bcrypt

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd Kuesioner
```

2. Create virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Configure environment:
```bash
cp .env.example .env
# Edit .env with your database credentials
```

5. Create MySQL database:
```sql
CREATE DATABASE kuesioner CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

6. Run migrations:
```bash
alembic upgrade head
```

7. Create admin user (via Python shell or API):
```python
from app.auth import get_password_hash
# Insert admin with hashed password
```

8. Run the server:
```bash
uvicorn app.main:app --reload
```

## Project Structure

```
Kuesioner/
├── app/
│   ├── main.py              # FastAPI app entry point
│   ├── config.py            # Configuration settings
│   ├── models.py            # SQLAlchemy models
│   ├── database.py          # Database connection
│   ├── schemas.py           # Pydantic schemas
│   ├── auth.py              # Authentication utilities
│   ├── routers/             # API endpoints
│   └── templates/           # Jinja2 templates
├── static/                  # CSS and JS files
├── alembic/                 # Database migrations
└── tests/                   # Test files
```

## Satisfaction Index Calculation

```
Satisfaction Index = (Total Score / Maximum Possible Score) × 100

Categories:
- 0-60%: Tidak Puas (Not Satisfied)
- 61-80%: Cukup (Satisfactory)
- 81-100%: Puas (Satisfied)
```

## API Endpoints

### Customer (Public)
- `GET /` - List active questionnaires
- `GET /survey/{id}` - Display survey form
- `POST /survey/submit` - Submit survey answers

### Admin (Protected)
- `POST /admin/login` - Admin login
- `GET /admin/dashboard` - Statistics overview
- `GET /admin/questionnaires` - List questionnaires
- `POST /admin/questionnaires` - Create questionnaire
- `PUT /admin/questionnaires/{id}` - Update questionnaire
- `DELETE /admin/questionnaires/{id}` - Delete questionnaire
- `GET /admin/results/{id}` - View survey results

## License

MIT License
# Survey-Kepuasan-Pelanggan
