Okay, it's time to fire up the Django development server and see your work come to life\!

Here's the content for your next Markdown file (e.g., `07-running-server-testing.md`):

-----

# Part 7: Running the Development Server & Testing

This is the moment of truth\! We'll start Django's development server and verify that everything is connected, your models are recognized, and your data from PostgreSQL is accessible via the Django Admin.

-----

### 7.1 Run the Django Development Server

The `runserver` command starts a lightweight web server provided by Django, suitable for development.

1.  **Ensure your virtual environment is active** and you are in your project's root directory (`django_pg_project/`, where `manage.py` is located).

2.  **Execute the `runserver` command:**

    ```bash
    python3 manage.py runserver
    ```

      * **Expected Output:** You should see messages indicating that the server is starting up, typically on `http://127.0.0.1:8000/`. You'll also see some system checks.

    <!-- end list -->

    ```
    Watching for file changes with StatReloader
    Performing system checks...

    System check identified no issues (0 silenced).

    You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for admin, auth, contenttypes, sessions.
    Run 'python3 manage.py migrate' to apply them.
    July 28, 2025 - 22:06:30
    Django version 5.0.7, using settings 'myproject.settings'
    Starting development server at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.
    ```

    *(Note: The "unapplied migration(s)" message might appear if you haven't run `python3 manage.py migrate` for Django's core apps yet, or if there are new Django updates. You can safely ignore it for now as long as your `myapp` migrations are handled.)*

#### **Troubleshooting: "Port is in use" (e.g., `8000`)**

If you get an error like `OSError: [Errno 98] Address already in use` or similar, it means another process is already using port `8000` on your system. This often happens if a previous instance of your Django server (or another web application) was not shut down cleanly.

  * **Reason:** Another program is occupying the default port `8000`.

  * **Solution 1: Find and Kill the Process (Recommended)**

      * **On Linux/macOS:**
        1.  Find the process ID (PID) using `lsof`:
            ```bash
            sudo lsof -i :8000
            ```
            You'll see output with `COMMAND`, `PID`, `USER`, etc. Note down the `PID` of the process (e.g., `12345`).
        2.  Kill the process:
            ```bash
            kill 12345   # Replace 12345 with the actual PID
            ```
            If it's stubborn, force kill:
            ```bash
            kill -9 12345
            ```
      * **On Windows (Command Prompt/PowerShell):**
        1.  Find the PID using `netstat`:
            ```cmd
            netstat -ano | findstr :8000
            ```
            The PID will be the last number in the line (e.g., `12345`).
        2.  Kill the process:
            ```cmd
            taskkill /PID 12345 /F   # Replace 12345 with the actual PID
            ```
      * After killing the process, try `python3 manage.py runserver` again.

  * **Solution 2: Run on a Different Port**
    If you can't or don't want to kill the process, you can tell Django to use a different port:

    ```bash
    python3 manage.py runserver 8001
    ```

    Then, you would access your server at `http://127.0.0.1:8001/` instead.

### 7.2 Access Django Admin

With the server running, it's time to log into the admin interface and see your models and data\!

1.  **Open your web browser.**

2.  **Navigate directly to the admin URL:**

      * If running on default port: `http://127.0.0.1:8000/admin/`
      * If running on a different port (e.g., 8001): `http://127.0.0.1:8001/admin/`

    **Do NOT** just go to `http://127.0.0.1:8000/`. That will show you the default Django welcome page because you haven't configured your project's root URL to point to anything specific yet.

3.  **Log in:**
    Use the superuser credentials (username and password) you created in Part 6.1.

4.  **Verify Your Models and Data:**

      * After logging in, you should see the main Django Admin dashboard.
      * Under the "MyApp" section (or whatever your app is named), you should see your registered models listed (e.g., "Course Outcomes", "Courses", "Process Standards", "Programs", "Inventory Items"). These are the models that correspond to your PostgreSQL tables.
      * Click on one of your models (e.g., "Inventory Items" if you created that test data). You should now see the data that exists in your PostgreSQL table displayed within the Django Admin.
      * Try adding a new record or editing an existing one to confirm full functionality.

If you can successfully log in and interact with your data in the Django Admin, congratulations\! Your Django project is now successfully connected to and interacting with your existing PostgreSQL database.

-----

**Next up:** The final section, **Part 8: Handling Database Schema Changes (Adding a Column)**, which shows you how to integrate future changes made directly in PostgreSQL back into your Django models.

-----
