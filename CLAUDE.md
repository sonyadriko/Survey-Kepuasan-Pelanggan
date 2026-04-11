# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Customer satisfaction survey system built with FastAPI, MySQL, and Jinja2 templates. Public users fill out surveys (rating 1-5 stars or text), admins manage questionnaires and view satisfaction analytics.

## Development Commands

```bash
# Setup
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Run development server
python -m uvicorn app.main:app --reload

# Database migrations
alembic upgrade head
alembic revision --autogenerate -m "description"

# Create admin user via API (when server running)
# POST to http://localhost:8000/admin/register with {"username": "...", "password": "..."}

# Interactive API docs (when DEBUG=True)
open http://localhost:8000/docs
```

## Local Database Setup (XAMPP)

This project uses XAMPP's MySQL on macOS. Default config in `.env`:
- `DB_HOST=localhost`
- `DB_PORT=3306`
- `DB_USER=root`
- `DB_PASSWORD=` (empty - XAMPP default)

Start XAMPP MySQL before running the app. Access phpMyAdmin at `http://localhost/phpmyadmin`.

Database: `kuesioner` - create manually or tables auto-create in debug mode.

## Architecture

### Router Structure
- `app/routers/auth.py` - `/admin/login`, `/admin/register`, `/admin/logout`, `/admin/me`. Uses JWT tokens via `python-jose`. Protected routes use `require_admin` dependency.
- `app/routers/admin.py` - `/admin/dashboard`, `/admin/questionnaires/*`, `/admin/results/*`. All endpoints protected by `require_admin`. Contains `calculate_satisfaction_index()` function.
- `app/routers/customer.py` - Public survey endpoints: `GET /`, `GET /survey/{id}`, `POST /survey/submit` (rate-limited 10/min).

### Authentication Flow
1. Register/Login → JWT token returned
2. Include token in header: `Authorization: Bearer <token>`
3. Protected routes use `require_admin` dependency from `auth.py`
4. Password hashing with bcrypt via `passlib`

### Database Models (SQLAlchemy)
- `Admin` - admin users with username/password_hash
- `Questionnaire` - surveys, has `is_active` flag
- `Question` - belongs to questionnaire, type=`rating` or `text`, ordered by `order_number`
- `Respondent` - survey submission, optional name/contact/ip
- `Answer` - links respondent to question, has either `rating_score` (1-5) or `text_answer`

Cascade delete enabled: deleting questionnaire deletes its questions and all responses.

### Satisfaction Index Formula
```
(Total Score / Maximum Possible Score) × 100
Categories: ≤60% Tidak Puas | 61-80% Cukup | 81-100% Puas
```

Implemented in `app/routers/admin.py::calculate_satisfaction_index()`.

### Key Files
- `app/main.py` - FastAPI app, includes routers, serves static files, lifespan events create tables in debug mode
- `app/config.py` - Pydantic Settings, constructs `database_url` property
- `app/database.py` - SQLAlchemy engine, `get_db()` dependency for sessions
- `app/auth.py` - JWT token creation/verification, password hashing utilities
- `app/schemas.py` - Pydantic models for request/response validation

### Frontend
Jinja2 templates in `app/templates/`. Static files in `static/`. No frontend framework - vanilla JS in `static/js/survey.js` handles survey form submission.

## Environment Variables

Key vars in `.env` (see `.env.example`):
- `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` - MySQL connection
- `SECRET_KEY` - JWT signing (change in production)
- `DEBUG=True` - Enables `/docs`, SQL echo, auto-create tables
