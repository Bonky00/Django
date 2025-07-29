Let's continue with **Part 2: Django Project & App Configuration**. This section will guide the user through setting up their Django project and application to connect to the PostgreSQL database they prepared in Part 1.

Here's the content for your next Markdown file (e.g., `02-django-project-app-config.md`):

-----

# Part 2: Django Project & App Configuration

With your PostgreSQL database prepared, it's time to set up your Django project and application. This involves creating the project, adding your custom app, and configuring Django's settings to connect to your database.

-----

### 2.1 Start a New Django Project

Navigate to your project's root directory (`django_pg_project/` from Part 0), where your `.venv` folder is located, and ensure your virtual environment is active.

1.  **Create the Django Project:**

    ```bash
    # Make sure you are in the 'django_pg_project' directory
    # And your virtual environment is active (.venv)
    django-admin startproject myproject .
    ```

      * The `myproject` part is the name of your main Django project.
      * The `.` (dot) at the end tells Django to create the project files in the current directory, rather than creating an extra nested folder.

    This will create a structure like this:

    ```
    django_pg_project/
    ├── .venv/
    ├── myproject/          # Your main Django project folder (contains settings.py, urls.py)
    │   ├── __init__.py
    │   ├── asgi.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── manage.py           # Django's command-line utility
    ```

### 2.2 Create a New Django App (e.g., `myapp`)

Django projects are composed of "apps." An app is a self-contained module that handles a specific function (e.g., a blog app, a user management app). We'll create one for your database models.

1.  **Create the Django App:**
    ```bash
    python3 manage.py startapp myapp
    ```
    This will create a new directory named `myapp` within your `django_pg_project` folder:
    ```
    django_pg_project/
    ├── .venv/
    ├── myproject/
    ├── myapp/              # Your new Django app folder
    │   ├── migrations/
    │   │   └── __init__.py
    │   ├── __init__.py
    │   ├── admin.py
    │   ├── apps.py
    │   ├── models.py       # This is where your database models will go
    │   ├── tests.py
    │   └── views.py
    └── manage.py
    ```

### 2.3 Configure `settings.py`

The `settings.py` file is the heart of your Django project's configuration. We need to tell Django about your new app and how to connect to your PostgreSQL database.

1.  **Open `myproject/settings.py` in your code editor.**

2.  **Add `myapp` to `INSTALLED_APPS`:**
    Find the `INSTALLED_APPS` list and add `'myapp'` to it. This tells Django to include your application.

    ```python
    # myproject/settings.py

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'myapp', # <--- Add your app here
    ]
    ```

3.  **Configure `DATABASES` for PostgreSQL Connection:**
    Replace the default SQLite database configuration with your PostgreSQL details. Use the database name, user, and password you created in Part 1.2.

    ```python
    # myproject/settings.py

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'mydjangodb',         # Your PostgreSQL database name (e.g., mydjangodb, PostgresDB)
            'USER': 'django_user',        # Your PostgreSQL username (e.g., django_user)
            'PASSWORD': 'your_strong_password', # Your PostgreSQL password
            'HOST': 'localhost',          # Or the IP address if your DB is on a different machine/container
            'PORT': '5432',               # Default PostgreSQL port
        }
    }
    ```

      * **Important:** Replace `'mydjangodb'`, `'django_user'`, and `'your_strong_password'` with the actual values you used.
      * `'HOST': 'localhost'` is correct if PostgreSQL is running directly on your machine. If you're using Docker for PostgreSQL, this might need to be the Docker service name (e.g., `'db'`) or the container's IP address.

4.  **Set `DEBUG = True` (for Development):**
    Ensure `DEBUG` is set to `True` during development. This provides detailed error pages, which are very helpful for troubleshooting. Remember to set it to `False` in production.

    ```python
    # myproject/settings.py

    DEBUG = True # <--- Ensure this is True for development
    ```

5.  **Save `myproject/settings.py`**.

### 2.4 Initial Django Migrations

Before we introspect your existing database, we need to run Django's built-in migrations. This creates the necessary tables for Django's internal functionalities (like user authentication, sessions, and the admin site) in your PostgreSQL database.

1.  **Run Django's Migrations:**

    ```bash
    python3 manage.py migrate
    ```

    You should see output indicating that various migrations (e.g., `auth`, `admin`, `contenttypes`) are being applied to your `mydjangodb` database.

      * **Troubleshooting: `django.db.utils.OperationalError: connection refused` or `database "mydjangodb" does not exist`**
          * **Reason:** Django cannot connect to your PostgreSQL database.
          * **Solution:**
              * **Double-check `DATABASES` settings in `settings.py`**: Verify `NAME`, `USER`, `PASSWORD`, `HOST`, `PORT` are all correct and match your PostgreSQL setup.
              * **Is PostgreSQL running?** Ensure the PostgreSQL server is active (refer to Part 1.1).
              * **Firewall/Permissions:** Check if a firewall is blocking port `5432`. Also, ensure your PostgreSQL user has the necessary permissions to connect to the database (check `pg_hba.conf` if you're managing PostgreSQL manually, though `pgAdmin4` usually handles this well).

-----

**Next up:** We'll move to **Part 3: Introspecting the Database with `inspectdb`**, where Django will read your existing tables and generate models for them.

-----
