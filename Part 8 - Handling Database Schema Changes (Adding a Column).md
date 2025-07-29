Alright, let's wrap up this course with **Part 8: Handling Database Schema Changes (Adding a Column)**. This is a crucial skill when working with `managed = False` models, as it outlines the workflow for keeping your Django models in sync with your manually managed database schema.

Here's the content for your final Markdown file (e.g., `08-handling-schema-changes.md`):

-----

# Part 8: Handling Database Schema Changes (Adding a Column)

When working with an existing PostgreSQL database and `managed = False` Django models, you'll inevitably encounter situations where you need to modify your database schema directly (e.g., adding a new column via `pgAdmin4`). Since Django isn't managing these tables, you need a specific workflow to update your Django models and make the new column visible in your application and admin interface.

-----

### 8.1 Manually Add the Column in PostgreSQL (`pgAdmin4`)

First, perform the actual schema change in your PostgreSQL database. This is done outside of Django.

1.  **Open `pgAdmin4`** and connect to your database (e.g., `mydjangodb`).

2.  **Navigate to the table you want to modify.** For this example, let's assume you're adding a `notes` column to your `CourseOutcomes` table.

3.  **Right-click on the table** (`CourseOutcomes` in this case) and select **`Query Tool`**.

4.  **Execute the `ALTER TABLE` SQL command** to add your new column. For example, to add a `notes` column of type `TEXT` that can be null:

    ```sql
    ALTER TABLE "CourseOutcomes"
    ADD COLUMN notes TEXT NULL;
    ```

      * **Important:**
          * Replace `"CourseOutcomes"` with the exact name of your table (case-sensitive if your table names are quoted in PostgreSQL).
          * Replace `notes` with your desired new column name.
          * Choose the appropriate `TEXT`, `VARCHAR(X)`, `INTEGER`, `BOOLEAN`, etc., based on your data type.
          * Use `NULL` if the column can be empty; use `NOT NULL` if it must always have a value (and often you'll need a `DEFAULT` value if it's `NOT NULL` on an existing table).

5.  **Verify the column exists:** You can right-click on the table in `pgAdmin4` and select `Properties` -\> `Columns` to confirm the new column is listed.

-----

### 8.2 Manually Update `myapp/models.py`

Now, you need to tell Django about this new column by adding it to the corresponding model definition.

1.  **Stop your Django development server** (`Ctrl + C` in your terminal) if it's running.

2.  **Open `myproject/myapp/models.py` in your code editor.**

3.  **Locate the model class** that corresponds to the table you modified (e.g., `class Courseoutcomes(models.Model):`).

4.  **Add a new field definition** inside this class that exactly matches your new column in PostgreSQL.

    ```python
    # myapp/models.py

    class Courseoutcomes(models.Model):
        outcome_id = models.AutoField(primary_key=True)
        course = models.ForeignKey(
            "Courses",
            models.DO_NOTHING,
            db_column="course_id",
            blank=True,
            null=True,
        )
        outcome_name = models.CharField(max_length=50)
        outcome_description = models.TextField(
            blank=True, null=True, help_text="This is a description of the course outcome."
        )
        # ADD YOUR NEW COLUMN HERE:
        notes = models.TextField(blank=True, null=True) # Match field type and nullability

        class Meta:
            managed = False
            db_table = "CourseOutcomes"

        def __str__(self):
            return self.outcome_name or f"Outcome ID: {self.outcome_id}"
    ```

5.  **Save `myapp/models.py`**. **(Do not forget this crucial step\!)**

-----

### 8.3 Generate a New Django Migration

Even though `managed = False`, Django needs a migration to update its internal knowledge of your model's structure. This migration will contain an `AddField` operation.

1.  **Ensure your virtual environment is active** and you are in your project's root directory.
2.  **Make migrations for your app:**
    ```bash
    python3 manage.py makemigrations myapp
    ```
      * **Expected Output:** Django should detect the added field and create a new migration file in `myapp/migrations/`. You'll see output like:

        ```
        Migrations for 'myapp':
          myapp/migrations/000X_add_notes_to_courseoutcomes.py
            - Add field notes to courseoutcomes
        ```

        Note the `000X` will be the next sequential number (e.g., `0002`, `0003`, etc.).

      * **Troubleshooting: `No changes detected`**:

          * This means Django still isn't seeing your changes in `myapp/models.py`. **Go back to Part 4.2** and verify the file is saved, has no syntax errors, and the changes are actually present.

-----

### 8.4 "Fake" the New Migration

Since you already added the column in PostgreSQL, you don't want Django to try and add it again. You just need Django to record that this change has been "applied" to its internal migration history.

1.  **Identify the name of the new migration file** (e.g., `000X_add_notes_to_courseoutcomes`).
2.  **"Fake" the new migration:**
    ```bash
    python3 manage.py migrate myapp 000X_add_notes_to_courseoutcomes --fake
    # Replace 000X_add_notes_to_courseoutcomes with your actual migration file name
    ```
      * **What `--fake` does:** This tells Django to mark *only that specific migration* as applied in the `django_migrations` table without executing its corresponding database operations.

-----

### 8.5 Update `myapp/admin.py` (Optional, but Recommended)

If you want your newly added column to be visible and editable within the Django Admin interface, you'll need to update your `ModelAdmin` configuration.

1.  **Open `myproject/myapp/admin.py` in your code editor.**

2.  **Locate the `ModelAdmin` class for your modified model** (e.g., `CourseoutcomesAdmin`).

3.  **Add the new column to `list_display`** (to show it in the list of records) and `fields` or `fieldsets` (to make it editable in the add/change form).

    ```python
    # myapp/admin.py

    @admin.register(Courseoutcomes)
    class CourseoutcomesAdmin(admin.ModelAdmin):
        list_display = ('outcome_id', 'outcome_name', 'course', 'outcome_description', 'notes') # Add 'notes' here
        search_fields = ('outcome_name', 'outcome_description', 'notes') # Add 'notes' to search
        list_filter = ('course',)
        # If you're using 'fields' or 'fieldsets' for form layout:
        fields = ('course', 'outcome_name', 'outcome_description', 'notes') # Add 'notes' here too
    ```

4.  **Save `myapp/admin.py`**.

-----

### 8.6 Run Server and Verify

Finally, restart your server and check the admin to confirm the new column appears.

1.  **Run the Django development server:**

    ```bash
    python3 manage.py runserver
    ```

      * If you get "Port is in use", refer back to Part 7.1 for solutions.

2.  **Access Django Admin:**

      * Go to `http://127.0.0.1:8000/admin/` (or your custom port).
      * Log in and navigate to your `Courseoutcomes` model.
      * You should now see the `notes` column in the list view (if added to `list_display`) and on the add/change form when you edit or create a `Courseoutcome` record.

This concludes the course on connecting Django to an existing PostgreSQL database and managing schema changes. You now have a robust workflow for interacting with your legacy or external data sources using the power of Django\!

-----
