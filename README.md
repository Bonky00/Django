## Course Outline: Connecting Django to an Existing PostgreSQL Database

This course guides you through setting up a Django project to interact with an *existing* PostgreSQL database, including common pitfalls and troubleshooting steps.

### **Part 0: Prerequisites & Setup**

1.  **System Requirements**
    * Python 3.x installed (preferably 3.9+)
    * `pip` (Python package installer)
    * PostgreSQL installed and running
    * `psycopg2-binary` dependencies (libpq-dev on Debian/Ubuntu, postgresql-devel on Fedora/RHEL, or install with Homebrew on macOS)
    * `pgAdmin4` (or another PostgreSQL GUI client) for database management.
    * A code editor (VS Code, Sublime Text, PyCharm, etc.)
    * Git and GitHub account (optional, but recommended for version control)

2.  **Initial Project Setup**
    * Create Project Directory
    * Set up a Python Virtual Environment (`.venv`)
    * Install Django and PostgreSQL Driver (`psycopg2-binary`)

### **Part 1: Database Preparation**

1.  **Verify PostgreSQL is Running**
2.  **Create a Dedicated PostgreSQL Database and User**
    * Using `psql` command-line or `pgAdmin4`
    * Grant necessary privileges.
3.  **Ensure Your Database Has Existing Tables (Crucial for `inspectdb`)**
    * Confirm custom tables exist in `pgAdmin4` (not just `auth_` and `django_` tables).
    * *Troubleshooting: If `inspectdb` gives blank `models.py`*
        * How to create a simple test table (`inventory_item`) using `pgAdmin4` Query Tool.

### **Part 2: Django Project & App Configuration**

1.  **Start a New Django Project**
    * `django-admin startproject myproject .`
2.  **Create a New Django App (e.g., `myapp`)**
    * `python3 manage.py startapp myapp`
3.  **Configure `settings.py`**
    * Add `myapp` to `INSTALLED_APPS`.
    * Configure `DATABASES` for PostgreSQL connection.
        * `ENGINE`, `NAME`, `USER`, `PASSWORD`, `HOST`, `PORT`.
    * Set `DEBUG = True` (for development).
4.  **Initial Django Migrations**
    * `python3 manage.py migrate` (for Django's internal tables).

### **Part 3: Introspecting the Database with `inspectdb`**

1.  **Run `inspectdb` to Generate Models**
    * `python3 manage.py inspectdb > myapp/models.py`
    * *Troubleshooting: `django.db.utils.OperationalError` (connection issues)*
        * Check `settings.py` credentials, host, port.
        * Verify PostgreSQL server is running.
        * Check `pg_hba.conf` for host/user permissions.
    * *Troubleshooting: Blank `myapp/models.py` after `inspectdb`*
        * This means no user-defined tables found. (Refer back to Part 1.3)

### **Part 4: Refining Django Models (`myapp/models.py`)**

1.  **Review the Generated `models.py` File**
    * **`managed = False`**: Understand why this is crucial (Django won't create/alter tables).
    * **Primary Keys**: Verify correctly identified.
    * **Field Types**: Adjust `TextField` to `CharField(max_length=X)` etc., if needed.
    * **Relationships (`ForeignKey`)**:
        * **CRITICAL: Add `on_delete` for every `ForeignKey` field.**
            * Choose `models.CASCADE`, `models.PROTECT`, `models.SET_NULL`, or `models.DO_NOTHING`.
            * *Troubleshooting: `TypeError: ForeignKey.__init__() got multiple values for argument 'on_delete'`*
                * Reason: `models.DO_NOTHING` was present as a positional argument AND `on_delete=models.CASCADE` was present as a keyword argument.
                * Solution: Remove the duplicate `on_delete` (e.g., keep only `models.DO_NOTHING` if that's what `inspectdb` inferred).
        * Add `blank=True, null=True` if the foreign key can be empty.
    * **Add `__str__` methods to each model (Highly Recommended!)**
        * Ensure `__str__` methods return meaningful strings for *that specific model* using its *own* fields.
        * *Troubleshooting: `AttributeError: 'ModelName' object has no attribute 'some_field'` in `__str__`*
            * Reason: `__str__` tried to access a field (e.g., `self.id`, `self.program_name`) that doesn't exist on that model.
            * Solution: Update `__str__` to use existing fields (e.g., `self.course_id`, `self.course_title`).
2.  **Save `myapp/models.py`** (Crucial after every manual edit!)
    * *Troubleshooting: `makemigrations` says "No changes detected"*
        * Reason: File not saved, or a hidden syntax error.
        * Solution: Save the file. Run `python3 myapp/models.py` to check for syntax errors. Add a temporary, obvious change (e.g., `force_migration_field = models.BooleanField(default=False)`) to force detection.

### **Part 5: Django Migrations for Existing Database**

1.  **Generate Initial Migrations for Your App:**
    * `python3 manage.py makemigrations myapp`
    * This records your `myapp/models.py` state in migration files.
2.  **"Fake" the Initial Migrations:**
    * `python3 manage.py migrate --fake-initial`
    * This tells Django to mark these migrations as applied without running SQL (since tables already exist).

### **Part 6: Integrating with Django Admin**

1.  **Create a Django Superuser**
    * `python3 manage.py createsuperuser`
2.  **Register Your Models in `myapp/admin.py`**
    * Import all models: `from .models import Model1, Model2, ...`
    * Register each: `admin.site.register(Model1)`
    * (Optional but recommended): Use `ModelAdmin` classes for customization (e.g., `list_display`, `search_fields`).
3.  **Configure Project-Level URLs (`myproject/myproject/urls.py`)**
    * Ensure `path('admin/', admin.site.urls)` exists.
    * Add `path('my_app/', include('myapp.urls'))` (even if `myapp/urls.py` is empty initially).
4.  **Create App-Level URLs (`myapp/urls.py`)**
    * Create an empty `myapp/urls.py` with `urlpatterns = []` at least.

### **Part 7: Running the Development Server & Testing**

1.  **Run the Django Development Server**
    * `python3 manage.py runserver`
    * *Troubleshooting: "Port is in use" (e.g., `8000`)*
        * Reason: Previous server instance or another program is using the port.
        * Solution: Kill the process using `sudo lsof -i :8000` and `kill <PID>` (Linux/macOS) or `netstat -ano | findstr :8000` and `taskkill /PID <PID> /F` (Windows). Or run on a different port: `python3 manage.py runserver 8001`.
2.  **Access Django Admin:**
    * Go to `http://127.0.0.1:8000/admin/` (or `8001` if you changed the port).
    * Log in with your superuser.
    * Verify your database tables are listed under your app.
    * Click on them to view/add/edit data.

### **Part 8: Handling Database Schema Changes (Adding a Column)**

1.  **Manually Add the Column in PostgreSQL (`pgAdmin4`)**
    * Execute `ALTER TABLE table_name ADD COLUMN new_column_name data_type;`
2.  **Manually Update `myapp/models.py`**
    * Add the new field definition to the corresponding Django model class.
    * Match the `models.Field` type to the PostgreSQL column type.
3.  **Generate a New Django Migration**
    * `python3 manage.py makemigrations myapp`
4.  **"Fake" the New Migration**
    * `python3 manage.py migrate myapp --fake` (or `python3 manage.py migrate myapp <latest_migration_name> --fake`)
5.  **Update `myapp/admin.py` (Optional, but Recommended)**
    * Add the new field to `list_display`, `fields`, or `fieldsets` in the relevant `ModelAdmin` class.
6.  **Run Server and Verify**

---

This comprehensive outline covers all the steps and troubleshooting points we've addressed. You can structure your GitHub repo with a main `README.md` and perhaps sub-sections or separate files in a `docs/` folder for more detailed explanations of each part. Good luck!
