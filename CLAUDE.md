# CLAUDE.md - AI Assistant Guide for dj-start-plus

## Project Overview

**dj-start-plus** is a Django project starter script that automates the setup of Django projects following best practices from the WTTD (Welcome to the Django) course. It's designed for Brazilian Portuguese developers and includes production-ready configurations for Heroku deployment.

## Tech Stack

- **Framework**: Django (Python web framework)
- **Environment Management**: python-decouple
- **Database**: dj-database-url (supports PostgreSQL, SQLite)
- **Static Files**: whitenoise
- **Development Tools**: django-extensions, django-test-without-migrations, jupyter
- **Deployment**: Heroku (with gunicorn, psycopg2)
- **Email**: Django email with SMTP/SendGrid support

## Project Structure

```
dj-start-plus/
├── dj-script          # Main bash script that creates Django projects
├── env-sample         # Sample environment variables file
├── keygen.py          # Secret key generator utility
├── README.md          # Portuguese documentation
└── .gitignore         # Git ignore patterns
```

## How This Script Works

The `dj-script` bash script automates Django project creation:

1. Creates project directory and virtual environment
2. Installs Django and common dependencies
3. Generates requirements.txt and requirements-dev.txt
4. Creates .env file from env-sample
5. Runs `django-admin startproject`
6. Creates a `core` app inside the project
7. Sets up Heroku deployment files (Procfile, requirements.txt)
8. Initializes git repository
9. Generates basic README.md

### Usage

```bash
./dj-script project-name
```

This creates a new project in `./project-name/` directory.

## Key Files Reference

### dj-script:1-73
Main automation script. Key function:
- `pip-install-and-add-to-requirements()` - Installs packages and tracks them in requirements

### env-sample:1-11
Template for environment variables:
- `SECRET_KEY` - Django secret key (must be changed in production)
- `DEBUG` - Debug mode flag
- `ALLOWED_HOSTS` - Comma-separated list of allowed hosts
- Email configuration (SMTP settings)

### keygen.py
Generates cryptographically secure Django SECRET_KEY values.

## Configuration Details

### Environment Variables

The generated projects use python-decouple for configuration:

- **SECRET_KEY**: Must be set (use `python contrib/keygen.py`)
- **DEBUG**: Boolean, defaults to False
- **ALLOWED_HOSTS**: CSV format, e.g., "127.0.0.1,.localhost,.herokuapp.com"
- **DATABASE_URL**: Database connection string
- **EMAIL_BACKEND**: Django email backend class
- **EMAIL_HOST**: SMTP server
- **EMAIL_PORT**: SMTP port (integer)
- **EMAIL_USE_TLS**: Boolean for TLS encryption
- **EMAIL_HOST_USER**: SMTP username
- **EMAIL_HOST_PASSWORD**: SMTP password

### Default Settings (Generated Projects)

- Language: `pt-br`
- Timezone: `America/Sao_Paulo`
- Static files: Collected to `staticfiles/` via whitenoise
- Test runner: `django-test-without-migrations` for faster tests

## Common Tasks

### Modifying the Script

When editing `dj-script`:
- It's a bash script that creates new Django projects
- Test changes by running it with a test project name
- Be careful with paths - it uses `../` to copy files from dj-start-plus directory

### Adding New Dependencies

To add packages to generated projects, modify dj-script:
```bash
pip-install-and-add-to-requirements package-name
```

For production-only dependencies, add directly to requirements.txt (like gunicorn/psycopg2).

### Updating env-sample

When adding new configuration:
1. Add to `env-sample` with sensible defaults
2. Update README.md instructions
3. Document in settings.py modifications section

## Testing

This is a project template generator, not a Django project itself. To test:

1. Run the script with a test project name:
   ```bash
   ./dj-script test-project
   ```

2. Verify the generated project:
   ```bash
   cd test-project
   source .test-project/bin/activate
   python manage.py check
   python manage.py test
   ```

3. Clean up test projects after verification

## Deployment (for Generated Projects)

Generated projects are pre-configured for Heroku:

### Heroku Setup
```bash
heroku apps:create your-project-name
heroku config:set SECRET_KEY=`python contrib/keygen.py`
heroku config:set DEBUG=False
heroku config:set ALLOWED_HOSTS=.herokuapp.com
```

### SendGrid Email
```bash
heroku addons:create sendgrid:starter
heroku config:set EMAIL_BACKEND=django.core.mail.backends.smtp.EmailBackend
heroku config:set EMAIL_HOST=smtp.sendgrid.net
heroku config:set EMAIL_PORT=587
heroku config:set EMAIL_USE_TLS=True
heroku config:set EMAIL_HOST_USER=<sendgrid-user>
heroku config:set EMAIL_HOST_PASSWORD=<sendgrid-password>
```

### Deploy
```bash
git push heroku master
```

## Important Notes for AI Assistants

1. **This is NOT a Django project** - It's a script that generates Django projects
2. **Don't run Django commands** in this directory - there's no manage.py here
3. **Portuguese context** - README and comments are in Brazilian Portuguese
4. **File references** - The script copies files from the parent directory (`../`)
5. **Testing changes** - Always test by generating a new project and verifying it works
6. **Manual steps required** - After running the script, developers must manually:
   - Edit urls.py to add core.views routes
   - Create views in core/views.py
   - Create templates directory and index.html
   - Modify settings.py with the documented changes

## Development Workflow

When making changes to dj-start-plus:

1. Modify the relevant file (dj-script, env-sample, keygen.py)
2. Test by generating a new project: `./dj-script test-proj`
3. Verify the generated project works correctly
4. Update README.md if needed
5. Commit changes with clear messages
6. Clean up test projects

## Troubleshooting

### Script Issues
- Ensure dj-script has execute permissions: `chmod +x dj-script`
- Script expects env-sample and keygen.py in parent directory when running

### Generated Project Issues
- Virtual environment not activating: Check path and use `source` command
- Import errors: Ensure virtual environment is activated
- Database errors: Check DATABASE_URL in .env
- Static files not loading: Run `python manage.py collectstatic`

## Version Control

The dj-start-plus repository includes:
- The generator script and utilities
- Documentation

Generated projects create their own git repositories with appropriate .gitignore.

## Contact/Issues

This project automates Django setup for educational purposes based on WTTD course materials.
