# Part 6: Integrating with Django Admin

Django's built-in administrative interface is incredibly powerful for managing your data. Once your models are defined and migrated (even "faked"), you can easily expose them in the admin.

-----

### 6.1 Create a Django Superuser

To access the Django admin site, you need a superuser account. This account has full permissions to manage all registered models.

1.  **Ensure your virtual environment is active** and you are in your project's root directory (`django_pg_project/`, where `manage.py` is located).

2.  **Execute the `createsuperuser` command:**

    ```bash
    python3 manage.py createsuperuser
    ```

3.  **Follow the prompts:**

      * **Username:** (e.g., `admin`)
      * **Email address:** (optional, can be left blank)
      * **Password:** Enter a strong password (it won't echo to the screen).
      * **Password (again):** Re-enter to confirm.

    You should see `Superuser created successfully.` upon completion. Remember these credentials\!

-----

### 6.2 Register Your Models in `myapp/admin.py`

By default, Django models are not visible in the admin. You need to explicitly register them within your app's `admin.py` file.

1.  **Open `myproject/myapp/admin.py` in your code editor.**

2.  **Import your models and register them:**
    You need to import each model class that you want to manage in the admin. Then, use `admin.site.register()` for each.

    ```python
    # myapp/admin.py
    from django.contrib import admin
    # Import ALL the models you want to manage in the admin from your models.py
    from .models import Courseoutcomes, Courses, ProcessStandards, Programs, InventoryItem # Add all your models here!

    # Register your models here
    admin.site.register(Courseoutcomes)
    admin.site.register(Courses)
    admin.site.register(ProcessStandards)
    admin.site.register(Programs)
    admin.site.register(InventoryItem) # If you created this test table
    ```

3.  **Optional but Recommended: Customize with `ModelAdmin`**
    For more control over how your models appear in the admin (e.g., which fields are shown in the list, search functionality), use `ModelAdmin` classes. This replaces the simple `admin.site.register(YourModel)`.

    ```python
    # myapp/admin.py
    from django.contrib import admin
    from .models import Courseoutcomes, Courses, ProcessStandards, Programs, InventoryItem

    # Example for customizing the Courseoutcomes admin:
    @admin.register(Courseoutcomes) # This decorator automatically registers the model
    class CourseoutcomesAdmin(admin.ModelAdmin):
        list_display = ('outcome_id', 'outcome_name', 'course', 'outcome_description') # Fields to show in the list view
        search_fields = ('outcome_name', 'outcome_description') # Fields to allow searching by
        list_filter = ('course',) # Fields to allow filtering by

    # You can apply similar customizations for other models:
    @admin.register(Courses)
    class CoursesAdmin(admin.ModelAdmin):
        list_display = ('course_id', 'course_code', 'course_title') # Adjust to your actual fields
        search_fields = ('course_code', 'course_title')

    @admin.register(Programs)
    class ProgramsAdmin(admin.ModelAdmin):
        list_display = ('program_id', 'program_name') # Adjust to your actual fields
        search_fields = ('program_name',)

    # For simple models without much customization, the direct register is fine too:
    admin.site.register(ProcessStandards)
    admin.site.register(InventoryItem)
    ```

4.  **Save `myapp/admin.py`**.

-----

### 6.3 Configure Project-Level URLs (`myproject/myproject/urls.py`)

Django uses a URL dispatcher to determine which view function handles a given URL. The main URL configuration is in your project's `urls.py`.

1.  **Open `myproject/myproject/urls.py` in your code editor.**

2.  **Ensure `admin.site.urls` is included:**
    This line is typically present by default when you create a new Django project. It maps the `/admin/` URL path to Django's built-in admin site.

    ```python
    # myproject/myproject/urls.py
    from django.contrib import admin
    from django.urls import path, include # Ensure 'include' is imported

    urlpatterns = [
        path('admin/', admin.site.urls), # This line is essential for the admin
        # You can also add a path to include your app's URLs here (useful later)
        # path('myapp/', include('myapp.urls')),
    ]
    ```

3.  **Save `myproject/myproject/urls.py`**.

-----

### 6.4 Create App-Level URLs (`myapp/urls.py`)

While not strictly necessary for the admin site itself, it's good practice to create an app-level `urls.py` file. This helps keep your URL configurations organized and makes your app more reusable.

1.  **Create a new file named `urls.py` inside your `myproject/myapp/` directory.**

2.  **Add basic URL patterns to `myapp/urls.py`:**
    For now, it can be empty, but it's good to have the structure.

    ```python
    # myapp/urls.py
    from django.urls import path
    from . import views # Assuming you might add views later

    urlpatterns = [
        # Define app-specific URL patterns here, e.g.:
        # path('mydata/', views.my_data_view, name='my_data'),
    ]
    ```

3.  **Save `myapp/urls.py`**.

-----

**Next up:** We're finally ready to see everything in action in **Part 7: Running the Development Server & Testing**\!

-----
