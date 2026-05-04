# MyBlog — Django Blog Application

A full-stack personal blogging platform built with Python and Django. Users can register, log in, and create, edit, and delete their own blog posts with image uploads. Built following Django's MVT architecture and deployed live on Render with PostgreSQL and Cloudinary.

🔗 **Live Demo:** [myblog1-m8gk.onrender.com](https://myblog1-m8gk.onrender.com)

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python 3.11, Django 5.2 |
| Database (dev) | SQLite |
| Database (production) | PostgreSQL (Render) |
| Frontend | HTML5, CSS3, Bootstrap 5, Bootstrap Icons |
| Media Storage | Cloudinary |
| Production Server | Gunicorn |
| Static Files | WhiteNoise |
| Deployment | Render |
| Version Control | Git, GitHub |

---

## Features

- User registration, login, and logout with session management
- Full CRUD — create, read, update, and delete blog posts
- Image upload per post using Cloudinary cloud storage
- Role-based access control — only the post author can edit or delete their post
- Django admin panel for managing all users and posts
- Responsive UI with Bootstrap 5 and Bootstrap Icons
- Pagination — 5 posts per page on the home feed
- Flash messages for user feedback on actions
- Template inheritance with a shared base layout

---

## Project Structure

```
myblog-project/
├── myblog/                 # Project config
│   ├── settings.py         # All settings — reads from .env
│   ├── urls.py             # Root URL router
│   └── wsgi.py             # WSGI entry point for Gunicorn
├── blog/                   # Main app
│   ├── models.py           # Post model with ForeignKey to User
│   ├── views.py            # CBVs — List, Detail, Create, Update, Delete
│   ├── urls.py             # App URL patterns
│   ├── forms.py            # UserRegisterForm extending UserCreationForm
│   └── admin.py            # Admin panel configuration
├── templates/
│   ├── blog/               # All page templates
│   └── registration/       # Django auth templates (login)
├── media/                  # Local uploaded files (dev only)
├── staticfiles/            # Collected static files (production)
├── Procfile                # Render start command
├── runtime.txt             # Python version for Render
├── requirements.txt        # All pip dependencies
└── .env                    # Secret keys — not committed to Git
```

---

## Architecture — MVT Pattern

```
Browser Request
      ↓
  urls.py  →  View  →  Model (ORM)  →  PostgreSQL
                ↓
           Template  →  HTML Response  →  Browser
```

- **Model** — `Post` class with 7 fields, ForeignKey to Django's built-in `User`
- **View** — Class-Based Views using `ListView`, `DetailView`, `CreateView`, `UpdateView`, `DeleteView`
- **Template** — Bootstrap 5 HTML templates with Django template language

---

## Security

- CSRF protection on all POST forms via `{% csrf_token %}`
- XSS protection via Django's automatic template escaping
- SQL injection prevention via ORM parameterised queries
- `LoginRequiredMixin` — blocks unauthenticated access to create/edit/delete views
- `UserPassesTestMixin` — ensures only the post author can edit or delete their post
- Passwords hashed with PBKDF2-SHA256 — never stored as plain text
- `SECRET_KEY` stored in environment variables, never in source code
- `DEBUG=False` in production

---

## Local Setup

```bash
# Clone the repository
git clone https://github.com/dinamain/myblog.git
cd myblog

# Create and activate virtual environment
python -m venv venv
venv\Scripts\activate          # Windows
source venv/bin/activate       # Mac/Linux

# Install dependencies
pip install -r requirements.txt

# Create .env file
echo SECRET_KEY=your-secret-key > .env
echo DEBUG=True >> .env

# Run migrations
python manage.py makemigrations
python manage.py migrate

# Create admin user
python manage.py createsuperuser

# Start development server
python manage.py runserver
```

Visit `http://127.0.0.1:8000` in your browser.

---

## Environment Variables

| Variable | Description |
|---|---|
| `SECRET_KEY` | Django secret key for signing sessions and tokens |
| `DEBUG` | `True` for development, `False` for production |
| `DATABASE_URL` | PostgreSQL connection string (production only) |
| `CLOUDINARY_CLOUD_NAME` | Cloudinary account cloud name |
| `CLOUDINARY_API_KEY` | Cloudinary API key |
| `CLOUDINARY_API_SECRET` | Cloudinary API secret |

---

## Deployment

The app is deployed on **Render** with the following setup:

- **Web server:** Gunicorn (`gunicorn myblog.wsgi`)
- **Database:** PostgreSQL provisioned on Render
- **Static files:** WhiteNoise middleware serves compressed static files
- **Media files:** Cloudinary cloud storage — persists across redeploys
- **CI/CD:** Auto-deploys on every push to the `main` branch on GitHub
- **Start command:** `python manage.py migrate && python manage.py collectstatic --noinput && gunicorn myblog.wsgi --log-file -`

---

## Database Schema

**Post model**

| Field | Type | Notes |
|---|---|---|
| id | BigAutoField | Primary key, auto-generated |
| title | CharField(200) | Post title |
| content | TextField | Post body, unlimited length |
| author | ForeignKey → User | Many posts to one user, CASCADE on delete |
| date_posted | DateTimeField | Set on creation, never changes |
| date_updated | DateTimeField | Updates on every save |
| image | CloudinaryField | Optional cover image |

---

## API of URLs

| Method | URL | View | Auth |
|---|---|---|---|
| GET | `/` | PostListView | Public |
| GET | `/post/<id>/` | PostDetailView | Public |
| GET/POST | `/post/new/` | PostCreateView | Login required |
| GET/POST | `/post/<id>/update/` | PostUpdateView | Author only |
| GET/POST | `/post/<id>/delete/` | PostDeleteView | Author only |
| GET/POST | `/register/` | register | Public |
| GET/POST | `/accounts/login/` | Django auth | Public |
| GET | `/accounts/logout/` | Django auth | Login required |
| GET | `/admin/` | Django admin | Staff only |

---

## Author

**Dina Usman**
Built as a portfolio project demonstrating full-stack Django development, cloud deployment, and software engineering best practices.

🔗 GitHub: [github.com/dinamain](https://github.com/dinamain)
